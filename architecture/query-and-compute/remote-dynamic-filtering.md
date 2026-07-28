---
title: "Remote Dynamic Filtering"
owners: [discord9]
---

# Remote Dynamic Filtering

## Decision

Dynamic filters produced during frontend execution may be propagated to remote
region scans through query-scoped runtime state. Filter state is identified
within a query independently of region routing, and the frontend owns update
fanout while datanodes apply updates to their query-scoped scans.

Remote propagation is always an optimization, never a correctness dependency.

## Rationale

A join may discover selective probe-side constraints only after remote scans
have been planned. Propagating those updates can recover storage pruning
without making the logical query depend on an asynchronous control path.
Query-scoped ownership also gives watcher tasks and cleanup one lifecycle
without creating a long-lived global filter manager.

## Constraints

- Encoding, routing, transport, decoding, or application failures degrade to
  local-only filtering or disable the remote optimization.
- Propagation may be skipped when payload size, fanout cost, or expected
  pruning benefit does not justify it.
- Query completion, cancellation, and loss of consumers end registrations and
  trigger cleanup.
- Region and scan metadata route updates but do not define filter identity.
