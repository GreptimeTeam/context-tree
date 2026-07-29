---
title: "Coordination and Compute Placement"
owners: [discord9]
soft_links:
  - /architecture/query-and-compute/shared-query-runtime.md
  - /architecture/query-and-compute/distributed-query-pushdown.md
---

# Coordination and Compute Placement

## Decision

The metadata service owns durable flow definitions and placement, while
flownode owns runtime lifecycle, scheduling, dirty-window tracking, and
progress. Batching delegates query execution through the frontend query path
to region-serving datanodes and writes results to the sink through the same
database boundary. Streaming keeps its explicit dataflow workers in flownode.

## Rationale

This placement separates continuous coordination from durable source and sink
storage, reuses distributed query planning for heavy scans and aggregation,
and avoids making flownode a second database. It also permits coordination and
query capacity to evolve independently.

## Constraints

- Flownode runtime state is not authoritative source or result data.
- Plan generation, execution, progress advancement, and dirty-window
  restoration are serialized per flow so concurrent triggers cannot consume
  the same progress range.
- Distributed continuous work must preserve the same partition and region
  correctness boundaries as an ordinary query.
- Flow query load shares datanode compute with ordinary queries, so placement
  and resource policy must account for that contention.
