---
title: "Stateless frontend tier"
owners: [discord9]
---

## Decision

Frontends form the stateless protocol and distributed-query tier. Any frontend
can accept supported ingestion or query protocols, obtain current table and
region routes from Metasrv, dispatch region-scoped work to Datanodes, and merge
the results. Durable table data and authoritative routing state remain outside
the frontend.

## Rationale

Keeping ingress and query coordination stateless allows horizontal replicas to
absorb connection and query concurrency without moving stored data. Pushing
eligible computation to the Datanodes that own the relevant regions reduces
data movement while leaving cross-region coordination at the frontend.

## Constraints

- Frontend route and metadata caches are accelerators; stale cache state must
  be refreshed from the control plane rather than treated as authority.
- Distributed plans must preserve query semantics when splitting work by
  region and merging partial results.
- Frontend loss must not lose durable database state, so persistent ownership
  cannot be introduced into this tier.

## Cross-Domain

Query-and-compute owns plan transformation semantics; this node records only
the frontend's placement and state boundary.
