---
title: "Snapshot-Fenced Window Repair"
owners: [discord9]
---

# Snapshot-Fenced Window Repair

## Decision

Dirty time windows are repaired in full-snapshot mode against one frozen
region-watermark boundary. Repair may be chunked, but every chunk must execute
under and return the same boundary. New writes arriving after the fence remain
in the live dirty queue for later work.

The frozen boundary becomes the incremental checkpoint only after all fenced
windows complete with matching watermark evidence.

## Rationale

Repairing chunks against moving snapshots can mix different source versions,
lose writes between chunks, or promote progress beyond data that was actually
recomputed. A fixed fence makes the multi-query repair equivalent to one
consistent repair while allowing new work to accumulate independently.

## Constraints

- A stale fence, failed query, or mismatched region watermark abandons the
  repair and restores unfinished windows for a fresh fenced attempt.
- Completed chunks are not treated as proof for a different fence.
- Checkpoint promotion waits until the fenced backlog is drained.
- Failure handling never discards not-yet-repaired dirty windows or advances
  progress.
