---
title: "Distributed Optimizer Boundary"
owners: [discord9]
---

# Distributed Optimizer Boundary

## Decision

Distributed-plan construction is an explicit optimizer phase boundary. Before
remote inputs are encapsulated, a deliberately narrow pre-pass exposes
side-local predicates and moves them into scans. Broad plan-shaping rules run
outside that boundary rather than reaching through remote subplans.

## Rationale

Region and storage pruning need predicates to be visible at scan planning time.
Once a remote subtree is represented as a merge input, ordinary optimizer
passes should not reshape its local/remote split. Keeping the pre-pass narrow
protects distributed semantics and keeps planning diagnostics intelligible.

## Constraints

- The boundary pass performs only the normalization and subquery rewrites
  required for safe filter pushdown.
- Limit, projection, distinct, and join-type rewrites do not enter this pass
  merely because they may reduce work; they can change the distributed
  boundary.
- Planner context that affects expression meaning, including a flow's logical
  evaluation time, must remain consistent in remote subplans.
- Failure to optimize an optional pushdown cannot weaken query correctness.
