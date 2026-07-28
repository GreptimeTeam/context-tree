---
title: "Shared Query Runtime"
owners: [discord9]
---

# Shared Query Runtime

## Decision

SQL and PromQL keep their own parsing and logical-planning semantics, then
converge on the same native DataFusion and Arrow execution substrate. The
frontend owns permissions, language planning, and read orchestration; protocol
servers own wire handling; the query engine executes the resulting plans.

## Rationale

A native PromQL path can reuse the database's operators, resource management,
distributed execution, and columnar batches. A separate evaluator or external
Prometheus execution component would add deployment and data-transport costs
and would divide resource control between engines.

## Constraints

- Language-specific selectors, range semantics, and functions remain explicit
  plan or expression extensions rather than being approximated as SQL text.
- Logical plans are deterministic and explainable before physical execution.
- Standalone and distributed deployments share the query engine; their
  distinction is local versus remote region access, not query semantics.
