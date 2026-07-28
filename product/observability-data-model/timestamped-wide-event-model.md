---
title: "Timestamped Wide-Event Model"
owners: [discord9]
---

# Timestamped Wide-Event Model

## Decision

Metrics, logs, and traces share a timestamped wide-event logical model. Each
event can carry typed dimensions and fields in a columnar table, and events
from different signals can be related with SQL.

## Rationale

Observability investigations follow entities and time across signal
boundaries. A common event model makes those relationships queryable without
first translating between unrelated storage models.

## Constraints

- The common model preserves the semantic identity of each signal rather than
  reducing every signal to an indistinguishable row shape.
- Model unification does not require unrelated signals to occupy the same
  physical table.
- Cross-signal relationships must be based on retained timestamps, attributes,
  and lineage rather than inferred solely from naming conventions.
