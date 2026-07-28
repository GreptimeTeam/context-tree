---
title: "DataFusion Fork Coupling"
owners: [discord9]
---

# DataFusion Fork Coupling

## Decision

DataFusion dependencies are resolved through a pinned project fork represented
in two coupled forms: exact public crate versions and matching fork revisions.
The declarations for all used DataFusion subcrates move as one dependency set.

## Rationale

Exact public versions provide coherent workspace dependency resolution while
the patch revisions select project-specific fork behavior. Updating only one
side can silently resolve an inconsistent or unintended engine.

## Constraints

- A newly used DataFusion subcrate is declared in both the workspace dependency
  set and the fork patch set.
- An upgrade updates the exact versions and matching fork revisions together
  for every DataFusion subcrate.
- Crates consume the shared workspace declarations rather than introducing
  independent version literals.
