---
title: "Retained operational events"
owners: [discord9]
soft_links:
  - /architecture/metadata-and-control-plane/durable-distributed-procedures.md
  - /architecture/metadata-and-control-plane/region-leadership-transfer.md
  - /architecture/storage-and-indexing/manifest-governed-object-reclamation.md
---

## Decision

Selected control-plane transitions and query diagnostics are recorded as
structured events in internal tables with configurable retention. Operators
may retain all event types, an explicit subset, or none.

Batch garbage collection uses sparse outcome reporting: routine lifecycle
transitions and no-op regions are omitted, while material per-region effects
and retry requirements remain queryable.

## Rationale

Logs alone make it difficult to reconstruct who changed durable state and how
a long-running operation progressed. Typed, queryable events preserve a
bounded operational timeline that can be correlated with database behavior.

Recording every selected region on every batch garbage-collection cycle would
turn that timeline into noise. Sparse reporting keeps the retained signal
proportional to actual effects, but known irreversible work must not disappear
merely because another peer fails or the procedure later retries.

## Constraints

- Omitting the event filter accepts current and future event types; an empty
  filter disables recording; a populated filter is an allowlist.
- Retention is finite and operator-controlled so diagnostics do not become an
  unbounded data stream.
- Events of one type keep a consistent structured schema.
- Batch garbage collection records successful region rows only when files or
  indexes were deleted or another GC pass is required; ordinary submitted,
  recovered, route-skipped, and no-op success paths stay silent.
- Completed per-region outcomes are retained across later procedure steps and
  terminal failure handling. Partial success from one peer is not discarded
  because another peer fails.
- Re-execution accumulates deleted-object evidence while each region's
  processed/retry status reflects its latest known outcome.
- Garbage-collection outcome rows retain the complete deleted file and index
  identities needed to reconstruct cleanup, rather than replacing them with a
  sampled or count-only summary.
- Recording is asynchronous and does not participate in the operation's
  commit; it is diagnostic evidence, not recovery authority or a transactional
  audit guarantee.
- Event-recorder backpressure or persistence failure is surfaced through
  telemetry without changing the outcome of the operation being observed.
