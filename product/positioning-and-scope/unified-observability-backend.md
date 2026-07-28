---
title: "Unified Observability Backend"
owners: [discord9]
---

# Unified Observability Backend

## Decision

GreptimeDB is a single storage and query backend for metrics, logs, and
traces. These signals share one database boundary instead of requiring an
independent backend for each observability pillar.

## Rationale

A common backend removes storage silos and makes cross-signal investigation a
normal database operation. It also lets operators consolidate signals without
giving up the distinctions that make each signal useful.

## Constraints

- Unification is a logical product boundary, not a requirement that every
  signal share one physical table.
- Collection, visualization, and alerting can remain ecosystem concerns; they
  should not require duplicating the underlying observability data.
- Signal-specific meaning must survive ingestion into the common backend.
