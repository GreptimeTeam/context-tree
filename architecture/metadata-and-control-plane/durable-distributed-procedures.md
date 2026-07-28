---
title: "Durable distributed procedures"
owners: [discord9]
soft_links:
  - /reliability-operations/availability-and-recovery/
---

## Decision

Multi-step control operations run as durable procedures: explicit state
machines whose recoverable state is persisted, whose conflicting resources are
locked, and whose independent work may be delegated to child procedures.

## Rationale

DDL and topology changes cross metadata and data nodes and cannot be made
atomic by one local call. Persisted state plus idempotent steps lets a new
Metasrv leader resume after interruption without abandoning a partially
applied cluster change.

## Constraints

- Every externally repeated step must be idempotent or able to recognize its
  already-applied result.
- State needed after restart is persisted before proceeding past the
  corresponding recovery boundary.
- Parent procedures wait for their child procedures before advancing.
- Resource locks serialize incompatible procedures but do not replace
  conditional metadata transactions.
- Rollback is procedure-specific; unsupported or best-effort rollback must not
  be represented as an atomic guarantee.
