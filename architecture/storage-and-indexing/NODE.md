---
title: "Storage and Indexing"
owners: [discord9]
---

# Storage and Indexing

GreptimeDB organizes storage around independently managed regions backed by an
object-storage-first LSM data plane. This domain records the durable boundaries
for persistence, file lifecycle, table multiplexing, compaction, and secondary
indexes.

## Leaves

- [Region storage boundary](region-storage-boundary.md)
- [Object-storage-first data plane](object-storage-first-data-plane.md)
- [Metric table multiplexing](metric-table-multiplexing.md)
- [Time-series compaction policy](time-series-compaction-policy.md)
- [Manifest-governed object reclamation](manifest-governed-object-reclamation.md)
- [SST-local secondary indexes](sst-local-secondary-indexes.md)
- [Background index construction](background-index-construction.md)
