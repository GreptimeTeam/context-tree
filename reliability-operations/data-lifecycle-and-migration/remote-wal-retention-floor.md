---
title: "Remote WAL retention floor"
owners: [discord9]
soft_links:
  - /architecture/metadata-and-control-plane/durable-distributed-procedures.md
  - /architecture/metadata-and-control-plane/automated-region-failover.md
---

## Decision

Remote WAL entries are pruned per shared topic only behind the lowest durable
replay position required by every region using that topic. Lagging regions are
flushed before the prune point advances, and the last pruned position is
persisted as a recovery floor.

## Rationale

Several regions can share one remote log, so the progress of the fastest
region cannot determine retention. A topic-wide minimum preserves enough log
for every region to restart while still allowing bounded storage reclamation.

## Constraints

- Pruning requires complete region-to-topic membership and durable progress
  observations; missing progress cannot be treated as permission to delete.
- The chosen prune point is below every participating region's required entry.
- Recovery never requests entries older than the persisted prune floor.
- Only one prune operation acts on a topic at a time, and overall pruning
  concurrency is bounded.
- Prune failures leave the safe floor unchanged and are retried through the
  durable procedure path.
