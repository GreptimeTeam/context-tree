---
title: "Workload-isolated execution"
owners: [discord9]
soft_links:
  - /architecture/query-and-compute/shared-query-runtime.md
---

## Decision

Asynchronous work runs on shared runtimes partitioned by workload, including
query, ingestion, compaction, heartbeat, and general coordination work.
CPU-heavy or synchronous-blocking work is moved to the corresponding blocking
pool instead of occupying an asynchronous worker.

## Rationale

A single saturated workload must not starve unrelated service-critical work.
Shared categorized runtimes provide one tunable governance point while
avoiding private executors with invisible capacity.

## Constraints

- New tasks use the category matching their workload rather than constructing
  an independent runtime.
- Blocking calls and heavy CPU work never execute directly on an asynchronous
  runtime worker.
- Runtime sizes are deployment controls, but increasing a pool does not
  replace memory, concurrency, or background-work limits.
- Synchronous `block_on` calls are prohibited inside asynchronous engine work
  because they can deadlock the runtime.
