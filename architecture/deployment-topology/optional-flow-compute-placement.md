---
title: "Optional flow-compute placement"
owners: [discord9]
---

## Decision

Continuous-flow computation is co-located with the other roles in standalone
mode and deploys as the optional Flownode role in distributed mode. A Flownode
is an independently configured compute service: flow-job metadata remains in
the control plane, and durable result tables remain in the normal data plane.

## Rationale

Continuous computation has a different CPU and memory profile from request
ingress and region storage. Making the distributed role optional and
independently scalable isolates that cost, while co-location preserves the
single-process operational model for small installations.

## Constraints

- Flownodes do not become owners of durable table storage or authoritative
  cluster metadata.
- Distributed Flownodes depend on Metasrv discovery and on reachable Frontend
  or data-plane services for their inputs and persisted outputs.
- Co-located flow work shares the standalone process's resources and failure
  domain.

## Cross-Domain

Continuous-processing owns flow execution and recovery semantics; this node
records only the role's deployment boundary.
