---
title: "Automated region failover"
owners: [discord9]
soft_links:
  - /reliability-operations/availability-and-recovery/
---

## Decision

The Metasrv leader detects loss of service at region granularity from datanode
heartbeats and restores availability by selecting a replacement and submitting
the standard region-migration procedure. Planned migration and automated
failover therefore share one fenced route-change mechanism.

## Rationale

A datanode can lose only part of its regions, so node-wide replacement is too
coarse. Reusing the migration procedure avoids a second recovery path with
different locking, catch-up, and metadata semantics.

## Constraints

- A region already migrating is not submitted for another failover.
- Maintenance mode suppresses automatic failover while preserving failure
  observations.
- Failed peers are excluded from replacement selection.
- Safe failover assumes shared storage and a remote WAL. Allowing failover
  from local-only WAL explicitly accepts possible data loss and is not the
  normal safety mode.
- Detection and control run only on the active Metasrv leader.
