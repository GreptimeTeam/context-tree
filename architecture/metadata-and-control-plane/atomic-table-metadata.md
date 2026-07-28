---
title: "Atomic table metadata"
owners: [discord9]
---

## Decision

A table's name, definition, route, datanode assignment, and related control
records form one logical metadata state. Operations that change several of
these views use one conditional transaction, with object locks for longer
multi-step operations.

## Rationale

Updating the views independently can expose a table name without a route, a
route without matching node ownership, or a stale topology overwrite.
Compare-and-update transactions make retries idempotent and reject changes
based on an obsolete snapshot.

## Constraints

- Metadata backends used by Metasrv must provide bounded atomic transactions
  and conditional comparisons.
- Callers must hold the appropriate catalog, schema, table-name, table, or
  region locks when an operation spans more than one transaction.
- A failed comparison must be treated as concurrent change or an already
  applied result; it must not be bypassed with an unconditional overwrite.
- Cache invalidation follows the complete committed state, not an intermediate
  metadata view.
