---
title: "Descriptive Table Semantics"
owners: [discord9]
---

# Descriptive Table Semantics

## Decision

Tables may carry a small, additive semantic description for facts that cannot
be recovered reliably from their schema or names. This description can
identify a signal and its source or lineage, and preserve meanings such as
field roles, units, temporality, and metadata quality.

## Rationale

Dashboards, alerts, and automated agents need machine-readable meaning without
guessing from column names. Keeping that meaning beside the table also lets it
survive ingestion pipelines that would otherwise discard it.

## Constraints

- Semantic metadata describes stored data; it does not coerce query behavior
  or replace the table schema.
- Ordinary tables remain valid without semantic metadata.
- Conflicting evidence is represented as mixed or unknown rather than
  collapsed into a fictitious single value.
- Consumers treat inferred or single-valued metadata as best-effort and honor
  its declared quality.
