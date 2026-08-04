---
title: "Region lifecycle publication fencing"
owners: [discord9]
soft_links:
  - /architecture/storage-and-indexing/manifest-governed-object-reclamation.md
  - /reliability-operations/resource-and-performance-governance/bounded-background-maintenance.md
---

# Region lifecycle publication fencing

## Decision

Region lifecycle operations may cooperatively cancel a running flush or local
compaction only before that job enters manifest publication. Cancellation and
publication start share one fence: if cancellation wins, the background job
reaches a terminal state before queued lifecycle work resumes; if publication
wins, the lifecycle operation waits for that state transition to complete.

## Rationale

Drop and truncate should not wait for expensive background work whose output
they will immediately invalidate. At the same time, interruption during a
manifest update can leave an ambiguous durable outcome and break the atomic
region view. A single publication boundary gives both paths a deterministic
owner and preserves request ordering around destructive lifecycle changes.

## Constraints

- A lifecycle request is validated against the current region state before it
  is allowed to disrupt background work.
- Once manifest publication may begin, the task is non-cancellable; a late
  cancellation request queues rather than interrupting the update.
- Cancellation stops and awaits spawned file writers before cleanup or a
  terminal cancellation notification, so late finalization cannot race
  object reclamation.
- Queued lifecycle requests retain their order and are revalidated when the
  background job ends. Waiting writes and unrelated DDL receive an explicit
  outcome rather than being silently released across the lifecycle boundary.

## Cross-Domain

Ownership and reclamation of unpublished or ambiguously published objects are
canonical in Manifest-governed object reclamation. Concurrency and memory
bounds for the background jobs remain canonical in Bounded background
maintenance.
