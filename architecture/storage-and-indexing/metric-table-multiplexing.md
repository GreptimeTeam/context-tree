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

## Rationale

A region and table per small metric series family carries disproportionate
metadata, memory, and storage overhead. Sharing physical regions amortizes that
cost and makes very large populations of Prometheus-style metric tables
practical while reusing the general-purpose storage engine.

## Constraints

- Routing must preserve logical table identity as it maps requests onto shared
  physical regions.
- Physical sharing limits independent per-logical-table settings such as TTL,
  partitioning, memtable sizing, and compaction policy.
- Workloads requiring storage or lifecycle isolation belong in independent
  Mito regions rather than the multiplexed metric layout.
- Schema and metadata changes must remain coherent for every logical table
  sharing the physical region.
