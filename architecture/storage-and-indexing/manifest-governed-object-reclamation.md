---
title: "Manifest-governed object reclamation"
owners: [discord9]
---

## Decision

Logical file removal and physical object deletion are separate lifecycle
steps. Region manifests record the live and removed file sets; datanode-side
garbage collection deletes objects only after checking current manifests,
temporary or cross-region file references, and a configurable lingering
period.

Routine collection can process manifest-tracked removals without listing the
whole object prefix. Explicit full-listing passes additionally reconcile
orphan objects that were never recorded or are no longer tracked.

## Rationale

Compaction, repartitioning, failed publication, and region drops can all leave
objects that are no longer part of the current region version. Immediate or
listing-driven deletion risks racing long queries and concurrent lifecycle
operations. Manifest evidence plus delayed reclamation makes deletion safe,
while separating routine and full scans contains object-store listing cost.

## Constraints

- Files referenced by a current manifest or protected reference set are never
  garbage-collection candidates.
- Known removed files wait out their lingering period; unknown files in active
  regions receive a conservative age threshold before deletion.
- Collection must operate against the authoritative region placement and must
  not delete through a follower's stale view.
- Data files and their versioned index sidecars are reclaimed consistently.
