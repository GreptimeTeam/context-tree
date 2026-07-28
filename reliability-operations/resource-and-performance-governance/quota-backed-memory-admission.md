---
title: "Quota-backed memory admission"
owners: [discord9]
---

## Decision

Memory-intensive paths reserve from an explicit quota before accepting work.
Request payloads share a server-wide pool, scans share a query pool, and
background tasks may have their own budgets. Exhaustion is handled by either
immediate rejection or a bounded wait; unlimited capacity is an explicit
configuration choice.

## Rationale

Unbounded acceptance turns burst pressure into process failure and hides the
source of contention. Admission converts memory scarcity into a visible,
retryable service outcome and lets operators choose between latency and
fail-fast behavior.

## Constraints

- A request larger than its entire pool is rejected rather than waiting.
- Waiting policies always have a deadline; they do not create an unbounded
  backlog.
- Acquired capacity remains held for the lifetime of the accounted work and
  is released on every completion or error path.
- Pool scope is preserved: one protocol cannot bypass the shared request
  budget, and one query cannot bypass the scan budget.
- Quota accounting is a protective estimate, not a promise that total process
  memory equals the configured limits.
