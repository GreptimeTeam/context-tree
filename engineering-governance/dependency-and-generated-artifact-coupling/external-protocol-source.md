---
title: "External Protocol Source"
owners: [discord9]
soft_links:
  - /engineering-governance/release-and-change-discipline/compatibility-change-discipline.md
---

# External Protocol Source

## Decision

Protocol and gRPC wire types are authored in the external `greptime-proto`
project. Wire-format changes are made there first, then adopted in GreptimeDB
through a dependency update and regeneration.

## Rationale

A single upstream schema keeps producers and consumers aligned across
repositories. Editing generated types locally would create an unrepeatable fork
of the wire contract and bypass compatibility review.

## Constraints

- Checked-in or build-generated protocol types are not edited as the source of
  a wire change.
- The upstream schema change preserves the persisted and wire compatibility
  contract.
- The downstream dependency update and compatibility evidence accompany the
  adoption of the new schema.
