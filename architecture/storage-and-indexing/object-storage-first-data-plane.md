---
title: "Object-storage-first data plane"
owners: [discord9]
---

## Decision

Distributed deployments use object storage as the primary durable home for
region data files and their derived index files. Memory and local-disk caches
accelerate writes and reads but are disposable performance layers. Datanodes
can consequently scale or be replaced without migrating the durable data
owned by those caches.

## Rationale

Object storage provides the cost and capacity profile required for long-lived
observability data, while separating compute from persistence allows read and
write capacity to scale independently. Tiered caching recovers low-latency
access without turning local disks into an ownership boundary.

## Constraints

- Cache absence, eviction, or loss must not change query correctness; the
  durable object remains the fallback.
- Publishing, replacing, and deleting immutable files must be coordinated
  through region versions rather than inferred from cache contents.
- Remote-object latency and listing cost are architectural inputs to caching,
  compaction, and garbage-collection policy.

## Cross-Domain

Deployment elasticity depends on this persistence boundary, while query
execution must remain correct across warm-cache and cold-object reads.
