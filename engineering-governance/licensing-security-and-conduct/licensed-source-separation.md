---
title: "Licensed Source Separation"
owners: [discord9]
soft_links:
  - /product/capability-boundaries-and-maturity/open-core-edition-boundary.md
---

# Licensed Source Separation

## Decision

Source files remain mechanically separated by license and build boundary.
Enterprise sources carry their commercial license, are selected only by the
enterprise build boundary, and are excluded from the default open-source
build; core sources retain the Apache-2.0 path.

## Rationale

The product's open-core edition promise is enforceable only when repository
layout, file headers, and build inclusion agree. A mechanically reviewable
separation prevents restricted functionality from entering the core by an
innocent file move or generated inclusion change.

## Constraints

- Every source file carries the header appropriate to its license class.
- Open-source exclusions and enterprise inclusions remain complementary when
  licensed files are added, moved, or generated.
- Enterprise files never receive an Apache header or enter the default build.
- Review of a licensed-source change checks both the file classification and
  its build reachability.
