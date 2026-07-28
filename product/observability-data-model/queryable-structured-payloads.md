---
title: "Queryable Structured Payloads"
owners: [discord9]
---

# Queryable Structured Payloads

## Decision

Semi-structured event attributes can be stored as validated, native JSON and
queried alongside typed table columns. Structured payloads are data, not
opaque strings that must be decoded outside the database.

## Rationale

Logs, traces, and telemetry attributes often evolve faster than a fixed
schema. Native structured values retain that flexibility while keeping
filtering and extraction inside the common query surface.

## Constraints

- Values entering a JSON-typed field must be valid JSON.
- Frequently queried, stable, or strongly typed attributes should use
  dedicated columns when their semantics justify a schema commitment.
- JSON complements typed tables; it is not a blanket replacement for them,
  because generic structure carries query and storage tradeoffs.
