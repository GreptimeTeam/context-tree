---
title: "Partition-aware write dispatch"
owners: [discord9]
soft_links:
  - /architecture/metadata-and-control-plane/control-plane-authority.md
  - /architecture/query-and-compute/query-data-access-boundary.md
---

## Decision

After normalization and schema reconciliation, the frontend write path splits
rows by the current table partition rule, resolves each region leader, groups
region requests by destination, and dispatches through region contracts.

## Rationale

External clients should not need to know region topology, and datanodes should
not independently claim rows. Centralized request partitioning sends each row
to its authoritative owner while batching work that shares a destination.

## Constraints

- Every row must map to exactly one region under a complete partition rule.
- Requests carry the partition-rule version used for routing so stale writers
  can be detected during topology change.
- Schema-derived defaults and canonical value conversions complete before
  region dispatch.
- Frontend reaches distributed storage through operator and region contracts,
  not datanode internals; standalone mode uses the equivalent local region
  boundary.
