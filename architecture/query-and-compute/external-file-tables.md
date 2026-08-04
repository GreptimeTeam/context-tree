---
title: "External File Tables"
owners: [discord9]
---

# External File Tables

## Decision

Files in object storage can be registered as immutable catalog tables and
queried through the ordinary SQL path. A database-owned file engine provides
decoding and scanning while catalog metadata gives the external data a stable
table identity and lifecycle.

SQL access to process-local files is deployment-scoped. Standalone deployments
confine local file tables and copy operations to an operator-configured
sandbox. Distributed deployments reject local SQL file paths and require
shared object storage instead.

## Rationale

Users can query existing file data without first importing it. Owning the file
data source inside GreptimeDB preserves integration with its cache, memory
management, catalog, and query planning; direct ad hoc registration in the
underlying query framework would bypass those controls.

Client-supplied SQL paths must not provide ambient access to the server host.
A single server-controlled local boundary protects database internals and
keeps standalone convenience from becoming a distributed correctness or
security dependency.

## Constraints

- This federation boundary covers external files and object storage, not a
  general contract for arbitrary remote databases.
- Every standalone local-file read or write resolves beneath the configured
  sandbox, including relative paths, absolute paths, and paths involving
  symbolic links; traversal outside the boundary is rejected.
- The sandbox must not expose GreptimeDB's data, WAL, manifest, or
  configuration files. A remote object-storage data home does not implicitly
  enable any local path.
- Distributed frontends and datanodes do not interpret SQL locations as local
  filesystem paths. Workflows shared across nodes use supported object
  storage.
- File records are treated as immutable; indexing and in-place mutation are
  not part of the external-table model.
- Filter and partition pruning depend on file format. A format without useful
  pushdown can require a full scan.
- Large sources that cannot prune effectively should be imported into managed
  storage rather than treated as efficient external scans.
