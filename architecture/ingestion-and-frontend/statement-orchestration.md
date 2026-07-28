---
title: "Statement orchestration"
owners: [discord9]
soft_links:
  - /architecture/query-and-compute/shared-query-runtime.md
---

## Decision

The frontend owns the common statement lifecycle: dialect-aware parsing,
interception, permission checks, execution controls, and dispatch. Statements
that require relational planning go through the shared query engine; direct
DDL and simple operations use the statement executor.

## Rationale

Choosing execution by statement semantics avoids both duplicating behavior in
wire servers and forcing simple operations through unnecessary logical
planning. One orchestration point also keeps cancellation, timeout, and access
policy consistent across entry protocols.

## Constraints

- Authorization is resolved before a statement performs catalog, data, or
  control-plane work.
- Parse and execution interceptors retain their defined ordering around
  permission and planning boundaries.
- The incoming wire protocol does not choose a separate query engine.
- Standalone and distributed deployments share parsing and query semantics;
  mode-specific executors are used only where the underlying action differs.
