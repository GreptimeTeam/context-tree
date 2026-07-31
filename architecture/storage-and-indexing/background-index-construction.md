---
title: "Background index construction"
owners: [discord9]
---

## Decision

Index construction supports both synchronous and asynchronous modes.
Asynchronous builds derive indexes from persisted SSTs after flush,
compaction, schema change, or an explicit rebuild request. A completed result
is installed into the region manifest only if both the exact source SST
generation and the schema generation used to build the index still match.
Those checks and the manifest update share one conditional publication
boundary.

## Rationale

Large index builds can suspend writes for an unacceptable duration. Moving
that work behind a scheduler preserves ingestion continuity and lets operators
bound its CPU, memory, and I/O separately, while the SST-local fallback
contract keeps queries correct until the index becomes available.

SST identity alone is not a sufficient publication fence: an index definition
can change while the underlying SST remains current. Fencing both generations
prevents a superseded index layout from becoming authoritative, while bounded
retry prevents an unrelated schema change from leaving a current SST
permanently unindexed.

## Constraints

- Asynchronous mode permits a delay between data visibility and index
  availability.
- Stale work caused by concurrent compaction, file replacement, or a newer
  schema/index definition must be aborted rather than publishing obsolete
  metadata.
- The index definition used by a build and its schema-generation fence derive
  from the same region version, while the SST-generation fence uses
  authoritative current manifest metadata.
- If the source SST is still current but only the schema generation advanced,
  the build is retried against the latest generation.
- For one SST, the scheduler keeps at most one active build and one coalesced
  pending successor; newer generations supersede older pending work so schema
  churn cannot create concurrent builds or an unbounded retry storm.
- Background builds are lower-priority, resource-intensive maintenance and
  must remain bounded relative to flush and compaction.
- Failed builds leave the base SST usable and can be retried without changing
  stored rows.
