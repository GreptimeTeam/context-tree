---
title: "Protocol ingress convergence"
owners: [discord9]
soft_links:
  - /architecture/deployment-topology/stateless-frontend-tier.md
---

## Decision

Supported database and observability protocols converge at the frontend on
shared request contexts and business-logic handlers. Protocol servers decode
network requests and encode responses; the frontend applies permissions,
interceptors, planning, ingestion policy, and routing.

## Rationale

Clients can keep ecosystem-native transports without giving each transport a
separate database implementation. Convergence makes authorization, catalog
semantics, and distributed routing consistent while retaining protocol-native
wire behavior.

## Constraints

- Wire codecs and network I/O remain in the protocol-server layer.
- Protocol adapters must hand requests to frontend handlers rather than
  reaching storage or metadata internals directly.
- A common query context carries catalog, schema, user, channel, and request
  policy across the shared path.
- Standalone and distributed modes preserve the same ingress semantics; only
  the final local-versus-remote region access differs.
