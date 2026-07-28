---
title: "Query Data-Access Boundary"
owners: [discord9]
---

# Query Data-Access Boundary

## Decision

Table metadata, query scanning, and region mutation are separate
responsibilities. The frontend and catalog provide schema, routing, and
statistics; the planner adapts scan-capable data sources into query-engine
table scans; procedures and protocol handlers send writes and lifecycle
operations to region servers.

Persistent base tables obtain their query data from regions. Views, temporary
tables, and generated relations may provide their own data sources without
pretending to own persistent regions.

## Rationale

A catalog table does not itself hold physical data, so making one abstraction
proxy metadata, reads, writes, alters, flushes, and compaction obscures
ownership. The split aligns each concern with the component that has the
required state and lets the query engine depend only on a scanning contract.

## Constraints

- The frontend reaches persistent storage through query, operator, catalog,
  and region contracts rather than datanode internals.
- Standalone mode may bridge to a local region server, but it preserves the
  same responsibility split used by remote access.
- Optimizers may consume schema, statistics, and filter-pushdown capability;
  they do not own storage mutation or table lifecycle.
