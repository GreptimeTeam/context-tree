---
title: "Externalized control-plane state"
owners: [discord9]
---

## Decision

In distributed mode, Metasrv owns cluster metadata, routing, placement,
repartitioning, coordinated procedures, and related security state. It
persists that authority through a pluggable shared key-value backend such as
etcd or a relational database rather than placing it in Frontends or
Datanodes.

Other service roles register and report health through Metasrv, then consume
its assignments and metadata through their control-plane clients.

## Rationale

Independent Frontends and Datanodes need one durable view of table topology and
node ownership. Externalizing this state makes service processes replaceable
and gives coordination and election a shared persistence boundary instead of
creating competing route authorities.

## Constraints

- Control-plane availability and durability depend on the selected shared
  backend and its election guarantees.
- A role that loses contact with Metasrv must not manufacture new placements
  or advance cluster-wide procedures from cached state.
- Every distributed role must advertise an address reachable by the peers
  that Metasrv directs to it.

## Cross-Domain

Metadata-and-control-plane owns the coordination rules themselves; this node
records their deployment and persistence boundary.
