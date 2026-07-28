---
title: "Resumable and verifiable snapshots"
owners: [discord9]
---

## Decision

Export and import V2 use a self-describing snapshot manifest containing schema
metadata, non-overlapping time-range chunks, progress state, and integrity
checksums. Each chunk is an independent retry unit, so interrupted export or
import resumes from durable progress and import verifies snapshot content
before writing it.

## Rationale

Large time-series datasets cannot be treated as one all-or-nothing transfer.
Time-bounded chunks cap retry cost, while an immutable manifest makes the
snapshot portable and verifiable without consulting the source cluster.

## Constraints

- Snapshot locations use explicit storage URI semantics; bare paths are
  rejected because client-side schema work and server-side data work may run
  on different hosts.
- Concurrent writers must not target the same snapshot path.
- Completed chunks are not repeated during resume, and an import state must
  match the same snapshot, target, catalog, and schema selection.
- V2 is not compatible with the V1 snapshot format and does not by itself
  guarantee cross-version schema migration.
- Time-based data chunking assumes tables have a usable time index; schema-only
  snapshots remain a distinct mode.
