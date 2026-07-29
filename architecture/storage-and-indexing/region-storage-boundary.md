---
title: "Region storage boundary"
owners: [discord9]
---

## Decision

Storage engines expose independently managed regions rather than partial table
objects. Mito is the default region engine, and each region owns its mutable
state, immutable SST set, schema and version metadata, and manifest. A table is
assembled above the engine from its region routes.

Datanodes therefore manage only the regions assigned to them; they do not
coordinate through a redundant table manifest shared by every placement of the
table.

## Rationale

A distributed table commonly spans multiple datanodes. Making each datanode a
writer of table-wide storage state creates multi-writer coordination and
duplicates metadata already held by the control plane. Region-scoped state
keeps persistence and lifecycle operations local while allowing routing,
placement, and failover to compose them into a table.

## Constraints

- Table-wide DDL, partitioning, and lifecycle operations must coordinate the
  affected regions above the storage engine.
- Region manifest changes must preserve an atomic, versioned view of the files
  visible to readers.
- Engine-specific schema and file metadata stay behind the region boundary.

## Cross-Domain

The metadata and control plane owns the table-to-region and region-to-node
relationships; storage owns the state of each routed region.
