---
title: "Authoritative Generated Sources"
owners: [discord9]
soft_links:
  - /engineering-governance/testing-and-quality/compatibility-regression-evidence.md
---

# Authoritative Generated Sources

## Decision

Generated artifacts are derived outputs, never independent sources of truth.
A change is made in the authoritative input and the affected artifacts are
regenerated and reviewed together.

## Rationale

Hand edits are overwritten by the next generation pass and can make checked-in
output disagree with the inputs future contributors will modify. Regeneration
preserves a reproducible relationship between intent and artifact.

## Constraints

- SQL expected results derive from test inputs and product behavior and are
  reviewed after regeneration.
- Configuration documentation derives from the example configurations and its
  template; dashboards derive from their generator inputs.
- Build-time generated code is changed through its generator or inputs rather
  than through transient build output.
- License headers are managed by the repository's classification and header
  tooling.
