---
title: "Signal-Scoped Cutovers"
owners: [discord9]
soft_links:
  - /product/positioning-and-scope/interoperable-adoption-surfaces.md
---

# Signal-Scoped Cutovers

## Decision

Adoption can proceed one observability signal or workflow at a time. Existing
collectors and consuming tools may remain while metrics, logs, or traces move
independently onto the shared GreptimeDB backend.

## Rationale

An all-at-once replacement couples unrelated teams and makes rollback
expensive. Protocol-compatible boundaries let users prove the new backend on a
bounded workload before consolidating more of the observability estate.

## Constraints

- Each cutover must retain the signal semantics and query behavior on which
  its consumers depend.
- Compatibility with an ingestion surface does not guarantee parity with
  every storage or query feature of the source system.
- Incremental adoption is a migration boundary, not a promise of live
  bidirectional replication between backends.
