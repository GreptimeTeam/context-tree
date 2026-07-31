---
title: "Native Histogram Semantics"
owners: [discord9]
soft_links:
  - /architecture/ingestion-and-frontend/protocol-compatibility-adapters.md
  - /engineering-governance/release-and-change-discipline/experimental-surface-gating.md
---

# Native Histogram Semantics

## Decision

A Prometheus native histogram is retained as one structured metric value at
each sample timestamp. Its sparse positive and negative buckets, zero bucket,
sum, count representation, bucket schema, custom bounds, and reset metadata
remain part of that value rather than being expanded into classic per-bucket
series.

Supported ingestion, persistence, query, and response paths share this
semantic contract. Native-histogram support remains an experimental surface
until the complete path is ready to become a compatibility commitment.

## Rationale

Expanding a native histogram into classic bucket series loses its compact
sparse layout and reset semantics while multiplying the series population. A
shared structured model lets storage and PromQL preserve the producer's
meaning without protocol-specific reinterpretation. Explicit experimental
gating keeps an incomplete end-to-end surface from being mistaken for a
stable public contract.

## Constraints

- Ingress validates the histogram as one coherent value before persistence;
  mixed count representations, unsupported bucket schemas, malformed spans or
  bounds, negative counts, and inconsistent totals are rejected.
- Persisted values retain enough information to distinguish integer and
  floating-point count families and exponential and custom bucket layouts.
- Query arithmetic reconciles only compatible bucket layouts and preserves
  reset-versus-gauge semantics. Unsupported combinations are not silently
  treated as ordinary float samples.
- Histogram result fields are additive and optional so float-only Prometheus
  responses retain their existing shape.
- The operator-facing enablement remains explicitly experimental and disabled
  by default until stabilization.
