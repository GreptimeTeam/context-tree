---
title: "External File Tables"
owners: [discord9]
---

# External File Tables

## Decision

Files in local or object storage can be registered as immutable catalog tables
and queried through the ordinary SQL path. A database-owned file engine
provides decoding and scanning while catalog metadata gives the external data
a stable table identity and lifecycle.

## Rationale

Users can query existing file data without first importing it. Owning the file
data source inside GreptimeDB preserves integration with its cache, memory
management, catalog, and query planning; direct ad hoc registration in the
underlying query framework would bypass those controls.

## Constraints

- This federation boundary covers external files and object storage, not a
  general contract for arbitrary remote databases.
- File records are treated as immutable; indexing and in-place mutation are
  not part of the external-table model.
- Filter and partition pruning depend on file format. A format without useful
  pushdown can require a full scan.
- Large sources that cannot prune effectively should be imported into managed
  storage rather than treated as efficient external scans.
