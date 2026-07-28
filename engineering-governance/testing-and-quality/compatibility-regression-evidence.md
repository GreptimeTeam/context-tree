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
applicable versions and topology, and accountable ownership.

## Rationale

Ad hoc release testing repeatedly misses upgrade regressions. Maintained cases
turn compatibility into attributable, repeatable evidence and keep the burden
with the feature that establishes the behavior.

## Constraints

- Setup runs against the earlier side of the version transition and verification
  runs against the later side, with version ranges expressing where the
  behavior applies.
- Expected output is reviewed as an assertion. Missing or changed golden output
  fails first and must never be accepted blindly.
- Cases are isolated from one another so order or shared namespace state cannot
  mask a compatibility failure.
- Coverage includes the topology in which the durable behavior is actually
  used.
