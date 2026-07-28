---
title: "Prometheus Query Continuity"
owners: [discord9]
soft_links:
  - /architecture/query-and-compute/shared-query-runtime.md
---

# Prometheus Query Continuity

## Decision

GreptimeDB accepts Prometheus metric writes and executes PromQL within its own
query runtime. Prometheus compatibility does not depend on embedding or
operating a separate Prometheus query engine.

## Rationale

Users can retain metric collection, dashboard, and alert expressions while
consolidating storage and execution into the database. Reusing the common
runtime also keeps resource management and distributed planning aligned with
SQL queries.

## Constraints

- PromQL remains a metric-native surface; SQL remains the broader analytical
  and cross-signal surface.
- Compatibility with upstream PromQL must be maintained deliberately as its
  semantics evolve.
- Unsupported PromQL behavior is outside the contract until it is implemented
  and tested; compatibility is not inferred from syntax alone.
