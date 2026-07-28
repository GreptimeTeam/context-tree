---
title: "Distributed write outcomes"
owners: [discord9]
soft_links:
  - /reliability-operations/availability-and-recovery/
---

## Decision

Permissions, structural validation, and schema preparation run before a
distributed write is dispatched whenever the external request requires
request-wide checks. After fan-out begins, an error does not imply that no
region committed; outcome and retry behavior must reflect confirmed work.

## Rationale

A frontend write spans independently responding region leaders rather than one
cross-region transaction. Treating every error as a total rejection invites
duplicate data on retry and produces false accepted or rejected counts for
protocols that expose partial success.

## Constraints

- Batch-wide preflight completes before the first batch is written when a
  protocol defines whole-request validation.
- A unit is accepted only after its primary table write is confirmed.
- Once dispatch may have committed data, retryable or unclassified failures
  are propagated unless the operation has an explicit idempotency guarantee.
- Auxiliary writes may add failure detail but do not retroactively reject a
  confirmed primary write.
- Partial-success reporting is used only where the external protocol defines
  it; other protocols retain their own success and error contract.
