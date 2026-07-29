---
title: "Background index construction"
owners: [discord9]
---

## Decision

Index construction supports both synchronous and asynchronous modes.
Asynchronous builds derive indexes from persisted SSTs after flush,
compaction, schema change, or an explicit rebuild request. A completed result
is installed into the region manifest only if its source SST is still present
in the current version and is not being compacted.

## Rationale

Large index builds can suspend writes for an unacceptable duration. Moving
that work behind a scheduler preserves ingestion continuity and lets operators
bound its CPU, memory, and I/O separately, while the SST-local fallback
contract keeps queries correct until the index becomes available.

## Constraints

- Asynchronous mode permits a delay between data visibility and index
  availability.
- Stale work caused by concurrent compaction or file replacement must be
  aborted rather than publishing metadata for an obsolete SST.
- Background builds are lower-priority, resource-intensive maintenance and
  must remain bounded relative to flush and compaction.
- Failed builds leave the base SST usable and can be retried without changing
  stored rows.
