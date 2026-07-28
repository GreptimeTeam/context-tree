---
title: "Protocol Fit by Workflow"
owners: [discord9]
soft_links:
  - /product/positioning-and-scope/interoperable-adoption-surfaces.md
  - /architecture/ingestion-and-frontend/protocol-compatibility-adapters.md
---

# Protocol Fit by Workflow

## Decision

Protocol choice follows the semantics of the adopting workflow rather than one
universal interface. OpenTelemetry is the native multi-signal intake,
Prometheus Remote Write and PromQL retain metric-native workflows, and SQL is
the common surface for relational and cross-signal analysis.

## Rationale

Keeping familiar signal and query models lets existing collectors, clients,
and dashboards move to GreptimeDB without first translating every workflow.
The shared database remains the convergence point behind those interfaces.

## Constraints

- Supported protocols share authorization, catalog, routing, and storage
  semantics; they are not independent database implementations.
- Compatibility covers the documented and tested surface, not every feature of
  the system that originated a protocol.
- Semantics that cannot be represented safely must produce an explicit
  limitation or error rather than a silent reinterpretation.
