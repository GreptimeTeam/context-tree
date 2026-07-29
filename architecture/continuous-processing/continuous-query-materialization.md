---
title: "Continuous Query Materialization"
owners: [discord9]
---

# Continuous Query Materialization

## Decision

A flow is a long-lived continuous or scheduled query that maintains its
derived result in a distinct sink table. Source data remains in its source
tables, internal aggregation state remains an execution concern, and consumers
read the materialized result through the ordinary table query path.

## Rationale

Separating ongoing computation from result storage lets reads avoid rerunning
the source query and lets the database recover or recompute flow state without
changing the consumer-facing table. It also keeps durable results under normal
storage and catalog guarantees.

## Constraints

- A flow cannot use the same table as both source and sink.
- Flow is a focused continuous-aggregation and materialized-view facility, not
  a general-purpose stream-processing system.
- Supported windows and operators bound the state and computation promised by
  the flow contract.
- Result maintenance goes through the normal frontend and storage path rather
  than creating a separate durable store inside flownode.
