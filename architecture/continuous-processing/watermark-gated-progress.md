---
title: "Watermark-Gated Progress"
owners: [discord9]
---

# Watermark-Gated Progress

## Decision

Batching enters incremental execution only for query shapes and scan bounds
whose correctness is proven. A checkpoint advances only from a successful
terminal result with a complete, non-regressing watermark for every
participating region. Missing or unprovable progress returns the flow to
full-snapshot processing without a partial advance.

## Rationale

Sequence-constrained scans can avoid recomputing old input, but skipping input
is safe only when storage proves exactly what each region covered. Business
rows alone do not establish that proof, and optimistic progress could make
omitted rows permanent in a materialized result.

## Constraints

- The incremental range is exclusive of the previous checkpoint and bounded
  above by the scan-open snapshot for each region.
- Watermarks are execution metadata, not synthetic columns in result rows.
- Stale cursors, execution errors, incomplete region maps, and unsupported
  aggregate shapes do not advance progress.
- Storage transitions that lose row-level sequence precision force
  recomputation rather than best-effort incremental exclusion.
- Region-specific bounds and terminal watermarks remain intact across the
  distributed query path.
