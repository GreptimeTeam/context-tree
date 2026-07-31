---
title: "HTTP Listener Surface Separation"
owners: [discord9]
soft_links:
  - /architecture/deployment-topology/stateless-frontend-tier.md
---

# HTTP Listener Surface Separation

## Decision

The historical full HTTP listener remains the compatibility surface for all
registered HTTP routes. Deployments may additionally enable an API listener
that exposes only the exact `/v1` and `/dashboard` namespaces from the same
finalized route registry.

Listener reachability and authentication are separate boundaries. A path
outside the API namespaces is absent from the API listener and returns
not-found before it can reach authentication or handler side effects.

## Rationale

Operators need a public data-plane listener without also publishing health,
status, metrics, configuration, debug, or other operational routes. Sharing
one route registry prevents built-in, plugin, and downstream routes from
drifting between listeners, while a namespace boundary preserves the
historical full listener and gives the API listener a small exposure contract.

## Constraints

- Every route under `/v1` is API-visible by definition. Internal or
  administrative routes that must not be public cannot use that namespace.
- The API boundary applies after all built-in, plugin, downstream, debug, and
  fallback routes have been registered, so later route injection cannot bypass
  it.
- Namespace matching respects path-component boundaries; lookalikes such as
  `/v10` and `/dashboard-admin` are not API routes.
- The full listener remains a superset and continues to serve `/v1` for
  backward compatibility.
- The two listeners share HTTP behavior and policy; enablement and bind address
  are deployment choices, not separate application semantics.
