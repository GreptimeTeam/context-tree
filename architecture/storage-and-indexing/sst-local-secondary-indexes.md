---
title: "SST-local secondary indexes"
owners: [discord9]
---

## Decision

Secondary indexes are derived, SST-local artifacts stored in a versioned
sidecar container. Per-file, per-column metadata records which index families
are actually available. Inverted, full-text, bloom/skipping, and vector indexes
specialize candidate selection, but the base SST and the normal visibility,
filtering, and deduplication pipeline remain authoritative.

Queries use a compatible index to prune row groups or rows and fall back to the
ordinary scan whenever an index is absent, ineligible, unreadable, or
semantically mismatched.

## Rationale

Immutable-file locality aligns index construction, caching, replacement, and
cleanup with the SST lifecycle. Specialized index families avoid imposing one
large universal index on heterogeneous observability predicates: tag filters,
text search, selective equality checks, and nearest-neighbor search have
different data structures and cost profiles.

## Constraints

- Index failure or partial availability must affect performance, never data
  visibility or query correctness.
- Inverted indexes narrow predicate work to candidate row groups; vector
  indexes supply approximate candidates that still pass through ordinary row
  visibility and final query semantics.
- Sidecar metadata is self-describing and versioned so files built with
  compatible index or vector-engine versions can coexist during replacement.
- Index caches are accelerators and cannot become a correctness dependency.
