---
title: "Ownership-Routed Review"
owners: [discord9]
---

# Ownership-Routed Review

## Decision

Repository review is routed through a catch-all approval group with
path-specific ownership for specialized modules. Changes that cross module
boundaries require attention from every affected ownership area.

## Rationale

The default route prevents unowned changes, while path-specific routing brings
the domain expertise needed for high-blast-radius changes. This makes review
responsibility discoverable without relying on ad hoc maintainer selection.

## Constraints

- The repository-wide fallback remains effective for paths without a more
  specific rule.
- The most specific applicable path ownership guides specialist review.
- Cross-cutting changes do not treat approval from one module as a substitute
  for review of the other affected modules.
- Ownership routing is maintained in the source repository rather than copied
  into prose as a list of current individuals.
