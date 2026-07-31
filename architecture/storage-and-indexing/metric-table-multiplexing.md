---
title: "Metric table multiplexing"
owners: [discord9]
---

## Decision

The metric engine multiplexes many logical metric tables over shared physical
Mito regions, including shared data and metadata storage. It remains a wrapper
over Mito rather than reimplementing low-level file persistence. Logical tables
retain the table-facing DDL and query abstraction even though their rows share
a wide physical storage layout.

Cached logical-column metadata follows the physical metadata region's
consistency boundary. A cache fill may publish only in an ordering that also
covers metadata mutation and the corresponding invalidation, so an in-flight
stale snapshot cannot repopulate the cache after a committed change.

## Rationale

A region and table per small metric series family carries disproportionate
metadata, memory, and storage overhead. Sharing physical regions amortizes that
cost and makes very large populations of Prometheus-style metric tables
practical while reusing the general-purpose storage engine.

Invalidation by itself is insufficient when a cache miss is already loading an
older snapshot: that fill can otherwise publish after invalidation completes.
Ordering fills with writes makes either the old fill complete before the write,
or the write complete before a later fill reads the new state.

## Constraints

- Routing must preserve logical table identity as it maps requests onto shared
  physical regions.
- Physical sharing limits independent per-logical-table settings such as TTL,
  partitioning, memtable sizing, and compaction policy.
- Workloads requiring storage or lifecycle isolation belong in independent
  Mito regions rather than the multiplexed metric layout.
- Schema and metadata changes must remain coherent for every logical table
  sharing the physical region.
- Cache-fill publication and metadata write-plus-invalidation are serialized
  within the physical metadata region that defines the cache key.
- Synchronization remains scoped to that metadata region; an in-flight fill
  must not block mutations or reads for unrelated regions.
