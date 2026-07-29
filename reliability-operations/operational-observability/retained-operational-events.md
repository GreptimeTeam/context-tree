---
title: "Retained operational events"
owners: [discord9]
soft_links:
  - /architecture/metadata-and-control-plane/durable-distributed-procedures.md
  - /architecture/metadata-and-control-plane/region-leadership-transfer.md
---

## Decision

Selected control-plane transitions and query diagnostics are recorded as
structured events in internal tables with configurable retention. Operators
may retain all event types, an explicit subset, or none.

## Rationale

Logs alone make it difficult to reconstruct who changed durable state and how
a long-running operation progressed. Typed, queryable events preserve a
bounded operational timeline that can be correlated with database behavior.

## Constraints

- Omitting the event filter accepts current and future event types; an empty
  filter disables recording; a populated filter is an allowlist.
- Retention is finite and operator-controlled so diagnostics do not become an
  unbounded data stream.
- Events of one type keep a consistent structured schema.
- Recording is asynchronous and does not participate in the operation's
  commit; it is diagnostic evidence, not recovery authority or a transactional
  audit guarantee.
- Event-recorder backpressure or persistence failure is surfaced through
  telemetry without changing the outcome of the operation being observed.
