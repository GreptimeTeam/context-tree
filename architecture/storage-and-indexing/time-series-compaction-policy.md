---
title: "Time-series compaction policy"
owners: [discord9]
---

## Decision

Mito uses an LSM data path that flushes mutable data into immutable SSTs.
Compaction is region-scoped and time-window-aware: it consolidates overlapping
files and primary-key versions, applies delete and expiration semantics, and
produces replacement SSTs with refreshed derived indexes. Compaction work is
scheduled and resource-bounded separately from foreground ingestion.

## Rationale

Uncompacted level-zero files amplify reads because timestamp ranges and key
versions overlap. Organizing work around time windows matches the dominant
time-range access pattern, while merging versions and tombstones gives readers
a smaller, more ordered file set. Background resource controls prevent this
maintenance from overwhelming writes.

## Constraints

- Normal file selection respects time locality rather than indiscriminately
  combining unrelated windows.
- New files become visible only through a successful region-version change;
  failed or superseded outputs must not replace their inputs.
- Secondary indexes are derived from the replacement rows and follow the new
  SST lifecycle rather than being treated as independent source data.
- Expired-file removal remains valid even when no ordinary merge is selected.
