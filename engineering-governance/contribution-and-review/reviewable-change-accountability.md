---
title: "Reviewable Change Accountability"
owners: [discord9]
soft_links:
  - /engineering-governance/release-and-change-discipline/compatibility-change-discipline.md
---

# Reviewable Change Accountability

## Decision

Every proposed change must state its intent and rationale at a level
proportionate to its scope, and its author remains accountable for understanding
and defending the design and implementation. Tool assistance does not transfer
that responsibility.

## Rationale

Review serves both correctness and durable knowledge transfer. Opaque changes,
including generated code that the submitter cannot explain, consume scarce
review capacity while concealing assumptions and compatibility risk.

## Constraints

- A substantial change explains what changes, why it is needed, and the
  important design choices; small changes may remain concise.
- API, schema, data, or other compatibility implications are called out
  explicitly.
- Unknowns, limitations, and assumptions are disclosed, especially for
  AI-assisted work.
- A proposal should represent a coherent logical change that reviewers can
  evaluate independently.
