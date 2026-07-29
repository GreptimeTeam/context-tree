---
title: "Single-active Metasrv leadership"
owners: [discord9]
soft_links:
  - /architecture/deployment-topology/
  - /reliability-operations/availability-and-recovery/
---

## Decision

Only the elected Metasrv leader runs leader-sensitive control loops, including
distributed procedures, region supervision, and other cluster-wide
coordinators. A leadership transition stops those duties on the old leader,
clears leader-local state, loads authoritative metadata, and starts them on the
new leader.

## Rationale

A single active coordinator prevents competing automation from issuing
conflicting topology or lifecycle changes. Reconstructing from durable state
rather than transferring transient memory lets leadership remain replaceable.

## Constraints

- Leader-local caches and in-memory coordination state are disposable and
  must be reset on every leadership change.
- Recoverable work must persist outside the leader process before it depends
  on surviving a transition.
- If no election service is configured, deployments must run exactly one
  Metasrv instance.
