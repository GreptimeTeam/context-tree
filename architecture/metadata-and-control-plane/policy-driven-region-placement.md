---
title: "Policy-driven region placement"
owners: [discord9]
soft_links:
  - /architecture/deployment-topology/
---

## Decision

Metasrv allocates regions through a replaceable selection policy rather than
embedding placement in DDL or failover logic. Policies may distribute turns,
consider live leases, or account for reported load; callers can exclude peers
and pass placement context.

## Rationale

Creation, migration, and recovery share the need to choose eligible datanodes
but optimize for different conditions. A common policy boundary keeps those
operations consistent while allowing deployment-specific placement behavior.

## Constraints

- Only live nodes that accept the requested workload are eligible.
- Recovery placement excludes known failed peers and must return a target for
  every region before migration begins.
- Multiple regions may intentionally share a node; allocation does not imply
  replica diversity or one-region-per-node placement.
- Placement is a control-plane decision; datanodes do not independently claim
  table regions.
