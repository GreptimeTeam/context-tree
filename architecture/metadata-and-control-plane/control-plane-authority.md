---
title: "Control-plane authority"
owners: [discord9]
soft_links:
  - /architecture/deployment-topology/
---

## Decision

In distributed deployments, Metasrv is the cluster authority for catalog
metadata, table routes, region allocation, repartitioning, and automated
region control. Durable state lives in a transaction-capable metadata backend;
frontend and datanode views are consumers or caches of that authority.

## Rationale

Keeping topology and catalog decisions outside independently scalable data and
query nodes gives the cluster one place to arbitrate changes. A pluggable
backend preserves this boundary across deployment environments without making
the control plane depend on one storage product.

## Constraints

- A route or catalog change is authoritative only after its metadata
  transaction commits.
- Derived route caches must be invalidated after a committed change and must
  never become an independent source of truth.
- Standalone mode may co-locate these responsibilities, but it preserves the
  same distinction between metadata authority and data-plane execution.
