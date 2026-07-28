---
title: "Region failover safety envelope"
owners: [discord9]
soft_links:
  - /architecture/metadata-and-control-plane/automated-region-failover.md
  - /architecture/metadata-and-control-plane/region-leadership-transfer.md
---

## Decision

Automatic region failover is an opt-in availability mode whose normal safety
envelope requires shared object storage and a remote WAL. A local-WAL
override is explicitly degraded operation: it may restore service while
losing writes that are unavailable on the replacement node.

## Rationale

Moving service to another datanode is safe only when that node can reconstruct
the authoritative durable state. Making the storage prerequisite explicit
prevents an availability control from being mistaken for a data-durability
guarantee.

## Constraints

- A replacement is not advertised writable until the coordinated leadership
  handoff reaches its durable cutover.
- Requests may fail transiently while a failed region is fenced and reopened;
  failover promises recovery, not an interruption-free transition.
- Maintenance mode suppresses automatic topology changes while operators are
  deliberately changing the cluster.
- Failure detection starts conservatively after cluster startup to avoid
  treating nodes that are still joining as failed.
- Enabling failover on local-only WAL requires an explicit acknowledgement of
  the data-loss risk.
