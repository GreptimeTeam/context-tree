---
title: "Telemetry-derived Entity Relationships"
owners: [discord9]
soft_links:
  - /architecture/query-and-compute/query-data-access-boundary.md
---

# Telemetry-derived Entity Relationships

## Decision

GreptimeDB represents observability entities and their relationships as
read-only relational views derived from ordinary telemetry tables. Entity
identity is declared alongside table semantics, while relationship rows are
time-ranged facts with explicit provenance. The resulting topology is queried
through SQL without a native graph store or a second copy of telemetry data.

The graph covers coarse observability topology such as service dependencies,
runtime placement, and containment. Request-level span and log causality stays
in the underlying telemetry and is correlated through bounded relational
queries rather than expanded into graph rows.

## Rationale

Metrics, logs, traces, and events already share one storage and query engine.
Deriving topology from those tables keeps newly observed entities immediately
visible and avoids a separately materialized catalog with its own ingestion,
backfill, freshness, and reconciliation lifecycle.

A relational surface also preserves GreptimeDB's product boundary: topology
supports incident localization and bounded traversal without turning the
database into a general-purpose graph system.

## Constraints

- Entity declarations are optional and additive; tables without them retain
  their ordinary storage and query behavior.
- Derivation runs with the caller's access rights. A computed relationship
  never reveals a source table, entity, or attribute the caller cannot read.
- Read-time derivation is bounded by a time window and inherits query
  cancellation and deadlines; an unbounded topology scan is not an implicit
  privilege or resource exemption.
- Relationships retain their observation window and provenance so declared,
  observed, and inferred evidence can coexist without one silently replacing
  another.
- Cross-signal correlation requires compatible entity identity. Display-name
  similarity alone is not a sufficient basis for merging entities.
