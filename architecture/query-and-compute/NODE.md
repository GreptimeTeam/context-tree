---
title: "Query and Compute"
owners: [discord9]
soft_links:
  - /architecture/continuous-processing/
---

# Query and Compute

Query-language, planning, execution, and distributed-compute decisions that
must remain coherent across engines and deployment modes.

## Leaves

- [Shared Query Runtime](shared-query-runtime.md) — SQL and PromQL converge on
  one native execution substrate without erasing language-specific semantics.
- [Distributed Query Pushdown](distributed-query-pushdown.md) — region-aware
  plans move legal computation to data and merge global results at the frontend.
- [Distributed Optimizer Boundary](distributed-optimizer-boundary.md) — filter
  preparation is kept narrow at the local/remote planning boundary.
- [Query Data-Access Boundary](query-data-access-boundary.md) — metadata,
  scanning, and region mutation have separate responsibilities.
- [External File Tables](external-file-tables.md) — files remain external while
  participating in the catalog and SQL query path.
- [Remote Dynamic Filtering](remote-dynamic-filtering.md) — runtime filters may
  prune remote scans but never become a correctness dependency.

## Cross-Domain

Long-lived flow materialization, execution-mode selection, and incremental
progress are canonical in [Continuous Processing](../continuous-processing/).
