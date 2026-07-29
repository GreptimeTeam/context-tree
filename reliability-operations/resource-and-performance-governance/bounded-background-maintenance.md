---
title: "Bounded background maintenance"
owners: [discord9]
soft_links:
  - /architecture/storage-and-indexing/background-index-construction.md
  - /architecture/storage-and-indexing/manifest-governed-object-reclamation.md
---

## Decision

Flush, compaction, purge, garbage collection, and asynchronous index work are
bounded by configurable concurrency and, where material, memory budgets.
Background work may be delayed or rejected under pressure rather than
overwhelming foreground reads and writes.

## Rationale

Maintenance preserves long-term performance and storage hygiene but can
consume the same CPU, memory, and I/O needed for serving requests. Explicit
bounds make that competition predictable and keep a maintenance surge from
becoming a service outage.

## Constraints

- Background schedulers do not create unbounded task or queue growth.
- Work that cannot acquire its permit or budget follows an explicit
  fail-or-bounded-wait policy.
- A failed or deferred index build leaves the underlying SST usable.
- Lowering or disabling maintenance capacity can accumulate compaction,
  indexing, WAL, or object-reclamation debt; it does not make that work
  unnecessary.
- Concurrency is bounded independently at the control-plane scheduler and the
  datanode worker where both layers can fan out work.
