---
title: "Region leadership transfer"
owners: [discord9]
soft_links:
  - /reliability-operations/data-lifecycle-and-migration/
---

## Decision

Moving a region between datanodes is a coordinated leadership handoff. Metasrv
marks the route as transitioning, prevents stale routing from remaining
authoritative, demotes or fences the old leader, brings the candidate to the
required durable position, promotes it, and then commits the new route.

## Rationale

Changing a route before the candidate is ready risks unavailable reads or
writes; promoting it before the old leader is fenced risks two writers. A
persisted handoff orders the data-plane transition and the authoritative
metadata cutover while remaining recoverable.

## Constraints

- Region and table locks prevent a second topology procedure from racing the
  handoff.
- Route changes use the latest version and conditional updates so a recovered
  procedure cannot overwrite newer placement.
- The candidate must reach the required WAL position before promotion.
- Failure after demotion rolls metadata back to a safe leadership state when
  promotion cannot complete.
- Frontend route caches are invalidated at metadata transition boundaries.
