---
title: "Durable Flow Definition"
owners: [discord9]
---

# Durable Flow Definition

## Decision

The durable flow record preserves the query, query context, source and sink
identity, execution type, placement, options, lifecycle status, and resolved
schedule. Runtime dirty windows, repair state, and incremental checkpoints are
volatile and are reconstructed from durable source data after restart.

## Rationale

Persisting the declarative definition is sufficient to recreate a flow with
the same user-visible meaning while avoiding a second durability protocol for
rapidly changing execution state. Starting recovery from source truth is safer
than trusting a partially persisted progress cursor.

## Constraints

- Recovery recreates execution with the original query context and begins from
  a correctness-proven full snapshot before using incremental progress.
- A flow with unresolved source tables is persisted as pending only when the
  caller explicitly requests deferral; pending flows are not scheduled.
- Activation requires all source tables to resolve and a valid flownode
  placement.
- Persisted definition fields and status evolve compatibly across mixed
  versions and restarts.
