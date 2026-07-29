---
title: "Standard-Protocol Tooling Boundary"
owners: [discord9]
soft_links:
  - /architecture/ingestion-and-frontend/protocol-ingress-convergence.md
---

# Standard-Protocol Tooling Boundary

## Decision

Collectors, dashboards, database clients, and operational tools integrate
through supported public protocols and explicit data conventions. Reference
artifacts such as the Grafana dashboards use the same Prometheus and SQL
surfaces available to other tools rather than a privileged internal path.

## Rationale

Protocol-level integration keeps tools replaceable and lets their release and
deployment lifecycles remain independent of the database core. Reference
artifacts can demonstrate a working contract without coupling GreptimeDB to
one visualization or orchestration stack.

## Constraints

- A reference dashboard or client may require documented schemas, labels, or
  minimum tool capabilities; those requirements are part of that integration.
- First-party reference material does not imply support for every feature of
  the external tool.
- Operators, charts, plugins, and language clients that live in separate
  projects retain their own packaging and release boundaries.
