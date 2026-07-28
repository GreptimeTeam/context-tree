---
title: "Dynamic region placement"
owners: [discord9]
---

## Decision

Regions are the minimum units of data placement and region-local computation.
Metasrv assigns their authoritative placements to Datanodes, and Frontends
route work from that mapping. Placements are dynamic rather than permanently
bound to a particular Datanode.

Safe region relocation and automatic failover require shared durable storage
and a remote WAL. A local WAL leaves unflushed state tied to its host and
therefore does not provide the same failover guarantee.

## Rationale

Region-granular placement lets the control plane balance load and recover only
the affected portions of a table. Shared storage and remote recovery state let
a replacement Datanode reopen a region without copying ownership from the
failed process.

## Constraints

- A Datanode must hold the authoritative lease or active placement before it
  serves a region as writable.
- Region health and placement changes flow through Metasrv; nodes must not
  independently invent a replacement owner.
- Enabling failover with host-local WAL accepts a data-loss risk and is not a
  normal high-availability topology.
- Route changes must reach Frontends before writes resume against a relocated
  region.
