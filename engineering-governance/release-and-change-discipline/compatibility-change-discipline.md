---
title: "Compatibility Change Discipline"
owners: [discord9]
soft_links:
  - /reliability-operations/compatibility-and-upgrades/persisted-and-wire-format-evolution.md
  - /engineering-governance/testing-and-quality/compatibility-regression-evidence.md
  - /engineering-governance/dependency-and-generated-artifact-coupling/external-protocol-source.md
---

# Compatibility Change Discipline

## Decision

Any change to a persisted or inter-process representation is treated as a
compatibility change, not an internal refactor. Its author must identify the
affected version transition, evolve the authoritative schema or format
additively, and provide dedicated transition evidence before the change is
release-ready.

## Rationale

The operational compatibility promise depends on contributors recognizing
which edits cross a durable boundary. Making that review responsibility
explicit prevents a locally harmless-looking field or enum change from
silently invalidating rolling or mixed-version operation.

## Constraints

- The owning source definition changes before generated consumers.
- Existing field positions, enum identities, and meanings are not repurposed.
- The change identifies its supported directions and topology rather than
  claiming unrestricted compatibility.
- Review includes both the durable-format owner and the compatibility case that
  proves the intended transition.
- Mechanical regeneration or a passing same-version test is not sufficient
  evidence by itself.
