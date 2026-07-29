---
title: "Continuous Processing"
owners: [discord9]
---

# Continuous Processing

Correctness and execution choices for continuously maintained queries and
derived results.

## Leaves

- [Continuous Query Materialization](continuous-query-materialization.md) —
  flows maintain query results in distinct durable sink tables.
- [Execution Mode Selection](execution-mode-selection.md) — batching and
  streaming are explicit flow types with different execution contracts.
- [Coordination and Compute Placement](coordination-and-compute-placement.md) —
  flownodes coordinate work while reusing query and storage components.
- [Durable Flow Definition](durable-flow-definition.md) — definitions survive
  restart while volatile progress is reconstructed conservatively.
- [Deterministic Evaluation Schedule](deterministic-evaluation-schedule.md) —
  interval schedules retain stable time semantics across recovery.
- [Watermark-Gated Progress](watermark-gated-progress.md) — incremental
  checkpoints move only with complete region-level correctness evidence.
- [Snapshot-Fenced Window Repair](snapshot-fenced-window-repair.md) — dirty
  windows are repaired against one stable snapshot boundary.
