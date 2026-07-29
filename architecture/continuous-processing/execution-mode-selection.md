---
title: "Execution Mode Selection"
owners: [discord9]
---

# Execution Mode Selection

## Decision

Each flow has an explicit execution type. Batching is the default and primary
path: it marks affected time windows and periodically executes aggregation
queries. Streaming is an explicit alternative that maintains a row-difference
dataflow graph.

Full-snapshot and incremental execution are states within batching; they do not
switch the flow to the streaming engine.

## Rationale

Batching reuses the main query engine and favors debuggability and bounded
window recomputation. Streaming favors row-level incremental latency but has a
different plan, state, and worker model. Treating the modes as one transparent
engine would hide material operational and semantic differences.

## Constraints

- Omitting the flow type selects batching.
- Mode is part of the persisted flow definition, not inferred afresh from each
  execution.
- Features exposed across both modes must preserve equivalent user semantics
  even when their evaluation paths differ.
- Operators unsupported by a selected mode are not silently delegated to the
  other mode.
