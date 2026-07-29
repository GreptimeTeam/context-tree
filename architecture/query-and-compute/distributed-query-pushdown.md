---
title: "Distributed Query Pushdown"
owners: [discord9]
---

# Distributed Query Pushdown

## Decision

Distributed planning is aware of table partitions and regions. A region is the
routing and computation unit: the frontend divides a query into region
subplans, sends each subplan to the node serving that region, and merges the
returned columnar streams into the global plan.

Computation moves below the merge boundary only when operator algebra preserves
the result. Filters, projections, and partition-compatible work can move to the
data; global or non-commutative work remains above the merge.

## Rationale

Pushing legal work to regions reduces data movement and lets scans and partial
computation run in parallel across datasets too large for one frontend. The
merge boundary makes remote execution look like a scan to the remainder of the
query while preserving a single global result.

## Constraints

- Partial aggregates require a corresponding final merge, and transformed
  aggregates must retain their algebraic reconstruction.
- Sorts, joins, percentiles, and other pipeline breakers are not pushed merely
  because their inputs reside on several regions.
- Multi-region routing follows the node currently serving each region; query
  language does not expose physical placement.
- Unsafe pushdown must preserve correctness through a safe fallback or fail the
  query rather than silently change its meaning.
