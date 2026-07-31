---
title: "Release Validation Gates"
owners: [discord9]
soft_links:
  - /engineering-governance/testing-and-quality/change-proportional-verification.md
  - /engineering-governance/testing-and-quality/compatibility-regression-evidence.md
---

# Release Validation Gates

## Decision

Publishing release artifacts and images is gated by compatibility validation
and query-performance regression validation against the immutable release
candidate. Automatic tag and scheduled releases run both gates.

A manual release may bypass either gate only through an explicit, auditable
validation policy. A bypass is an acknowledged release decision, not an
implicit consequence of a failed, cancelled, or unexpectedly skipped job.

## Rationale

Ordinary build and test success does not prove that a candidate preserves
cross-version behavior or avoids material query regressions. Binding the gates
to the exact candidate prevents a moving branch from validating different code
than the artifacts being published. An explicit manual escape hatch permits
recovery from accepted regressions or unavailable infrastructure without
making validation failures silently publishable.

## Constraints

- Validation, artifact construction, and publication identify the same
  immutable candidate revision and an explicit comparison baseline.
- Failed, cancelled, or unexpectedly skipped validation blocks publication.
  A skipped gate is accepted only when the effective manual policy names that
  gate.
- Automatic releases cannot select a bypass policy.
- Manual policy summaries retain the effective policy, actors, trigger,
  candidate revision, and baseline for review.
- Recovery of a failed formal-tag release reuses and validates that tag rather
  than recreating it or allowing an older release to replace the latest one.
