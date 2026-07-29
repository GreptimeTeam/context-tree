---
title: "Prometheus-Shaped Metric Populations"
owners: [discord9]
soft_links:
  - /architecture/storage-and-indexing/metric-table-multiplexing.md
---

# Prometheus-Shaped Metric Populations

## Decision

Metric adoption preserves a large population of small, independently named
Prometheus-style metric tables as logical tables. Users retain ordinary table
creation, alteration, and query semantics even when GreptimeDB shares physical
storage underneath them.

## Rationale

Forcing every migrated metric into a manually consolidated schema would make
Prometheus adoption expensive and would expose storage mechanics to users.
Logical-table continuity keeps metric identity familiar while allowing the
database to amortize per-table overhead.

## Constraints

- Physical sharing limits independent per-logical-table controls such as
  lifecycle, partitioning, memory, and compaction settings.
- A workload that requires physical or lifecycle isolation belongs in a
  separately stored table rather than the multiplexed metric layout.
- Logical identity must remain stable across ingestion, routing, schema
  changes, and queries.
