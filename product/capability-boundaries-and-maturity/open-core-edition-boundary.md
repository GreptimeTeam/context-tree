---
title: "Open-Core Edition Boundary"
owners: [discord9]
soft_links:
  - /engineering-governance/licensing-security-and-conduct/licensed-source-separation.md
---

# Open-Core Edition Boundary

## Decision

The default GreptimeDB build is an independently usable open-source core
licensed under Apache License 2.0. Commercial capabilities are peripheral
extensions, separately licensed and admitted only through the explicit
enterprise feature boundary.

## Rationale

An explicit edition boundary keeps the core broadly usable and auditable while
allowing differentiated commercial capabilities. Build-time separation makes
the licensing consequence visible rather than implicit.

## Constraints

- Enterprise code is not compiled into the default open-source build.
- Files and modules under the commercial boundary must identify that license
  explicitly.
- Adding a commercial extension must not change the license or independent
  usefulness of the core.
