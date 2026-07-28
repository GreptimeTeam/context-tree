---
title: "Interoperable Adoption Surfaces"
owners: [discord9]
---

# Interoperable Adoption Surfaces

## Decision

GreptimeDB meets observability ecosystems through established ingestion and
query conventions. OpenTelemetry and compatible signal protocols feed the
backend, while SQL provides the cross-signal query surface and PromQL
preserves metric-native workflows.

## Rationale

Interoperability lets a team adopt the common backend one signal or workflow
at a time. Existing collectors and dashboards can remain in place while the
storage boundary is consolidated.

## Constraints

- A protocol surface carries only its documented compatibility contract; it
  does not imply behavioral parity with every feature of an upstream system.
- SQL is the common analytical language, while signal-native query semantics
  remain available where they are materially useful.
- Incremental adoption must not require an all-signals-at-once migration.
