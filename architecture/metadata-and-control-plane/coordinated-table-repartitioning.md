---
title: "Coordinated table repartitioning"
owners: [discord9]
soft_links:
  - /reliability-operations/data-lifecycle-and-migration/
  - /architecture/storage-and-indexing/
---

## Decision

Changing a table's partitioning is a Metasrv-coordinated durable procedure.
The control plane validates the requested rule, decomposes it into independent
split or merge groups, allocates required regions, stages route changes, and
collects the group results before final cleanup.

## Rationale

Partitioning changes both routing metadata and the data visible through each
region. Grouped, recoverable work limits the blast radius of failure, while a
narrow metadata cutover avoids holding ingestion for the full data movement.

## Constraints

- The target partition rule must cover the key space exactly once and be
  expressible as supported split or merge groups.
- A table-level write lock serializes the repartition with other metadata and
  topology changes.
- Metadata, allocated-region records, and failed or unknown groups are rolled
  back together; successful groups need not be discarded solely because
  another independent group fails.
- Route caches are invalidated only after table metadata and routes again form
  a consistent view.
- Manifest remapping, cross-region reads, and reclamation of unreferenced files
  remain storage-lifecycle responsibilities coordinated by this procedure.
