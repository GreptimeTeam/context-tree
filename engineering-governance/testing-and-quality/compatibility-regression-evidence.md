---
title: "Compatibility Regression Evidence"
owners: [discord9]
soft_links:
  - /engineering-governance/release-and-change-discipline/compatibility-change-discipline.md
---

# Compatibility Regression Evidence

## Decision

A change to persisted or wire-visible behavior must add or update a dedicated,
version-aware compatibility case. Each case records why it exists, its
applicable source and target versions, transition direction, topology, and
accountable ownership.

## Rationale

Ad hoc release testing repeatedly misses upgrade regressions. Maintained cases
turn compatibility into attributable, repeatable evidence and keep the burden
with the feature that establishes the behavior.

## Constraints

- Setup runs against the declared `from` version and verification runs against
  the declared `to` version. The source may be older for an upgrade or newer
  for a supported downgrade; version ranges express that direction explicitly.
- A transition expected to have dedicated coverage fails when filtering
  selects no matching case; an empty run is not compatibility evidence.
- Expected output is reviewed as an assertion. Missing or changed golden output
  fails first and must never be accepted blindly.
- Cases are isolated from one another so order or shared namespace state cannot
  mask a compatibility failure.
- Coverage includes the topology in which the durable behavior is actually
  used.
