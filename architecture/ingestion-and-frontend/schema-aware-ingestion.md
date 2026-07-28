---
title: "Schema-aware ingestion"
owners: [discord9]
soft_links:
  - /product/observability-data-model/
  - /architecture/metadata-and-control-plane/atomic-table-metadata.md
---

## Decision

Protocol-specific ingestion is normalized into a canonical row request with
explicit timestamp, tag, and field roles before storage dispatch. Endpoints
that opt into schema-on-write may create a missing table or add compatible
columns through the normal metadata and statement path.

## Rationale

Metrics, logs, and traces arrive in different shapes, and observability
attributes can be sparse or data-dependent. A shared row boundary lets one
schema validator and inserter protect storage while controlled evolution keeps
dynamic workloads usable.

## Constraints

- Input values and row shape are validated before they can influence persistent
  schema or reach a region.
- Existing table schema is authoritative where incoming values can be
  reconciled; incompatible values fail rather than silently changing meaning.
- Schema inference considers the complete planning unit, so invalid rows do
  not create columns before being rejected or isolated.
- Automatic creation requires both the server-side capability and the
  request's policy; it is never forced on all ingestion protocols.
- Metadata changes complete before rows are partitioned against the resulting
  table definition.
