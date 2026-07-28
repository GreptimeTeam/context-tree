---
title: "Deployment Topology"
owners: [discord9]
---

# Deployment Topology

GreptimeDB supports a co-located standalone topology and a distributed
topology whose ingress, storage, control, and optional continuous-compute roles
scale independently. This domain records where durable authority resides and
which placement constraints preserve correctness.

## Leaves

- [Dual deployment modes](dual-deployment-modes.md)
- [Stateless frontend tier](stateless-frontend-tier.md)
- [Dynamic region placement](dynamic-region-placement.md)
- [Externalized control-plane state](externalized-control-plane-state.md)
- [Optional flow-compute placement](optional-flow-compute-placement.md)
