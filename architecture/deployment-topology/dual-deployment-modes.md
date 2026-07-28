---
title: "Dual deployment modes"
owners: [discord9]
---

## Decision

The same database capabilities are offered through two deployment boundaries.
Standalone mode co-locates frontend, region storage, local metadata and
procedure services, and flow compute in one server instance. Distributed mode
separates Frontend, Datanode, Metasrv, and optional Flownode into independently
configured and scalable services.

Standalone is the operationally simple topology for development and small
deployments; distributed mode is the topology for horizontal scaling, role
isolation, and region relocation.

## Rationale

A single process removes service discovery and cluster coordination from the
small-deployment path. At larger scale, separating ingress, query coordination,
storage, metadata, and continuous compute prevents one workload's resource
shape from dictating the capacity of every other role.

## Constraints

- Standalone is one resource, restart, upgrade, and failure domain and does not
  provide distributed region placement or failover.
- Distributed roles require reachable advertised service addresses and a
  shared authoritative control plane.
- Deployment mode changes coordination and failure boundaries, not the
  user-facing table and query model.
