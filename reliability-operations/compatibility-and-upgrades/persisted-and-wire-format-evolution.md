---
title: "Persisted and wire format evolution"
owners: [discord9]
---

## Decision

Persisted data and internal wire formats evolve additively within their
declared compatibility ranges. Existing fields, enum identities, and version
counters keep their meanings; new readers supply defaults for older data, and
supported older readers tolerate additions made by newer writers.

An internal model may become more strongly typed without changing its durable
boundary. Within a supported downgrade range, writers retain an encoding that
the older reader understands while readers accept both the legacy and newer
forms needed during the transition.

## Rationale

Manifests, WAL records, object metadata, metadata-store values, and messages
outlive the process that produced them. Rolling or mixed-version operation is
safe only when either side can encounter artifacts produced by the other
without reinterpreting existing state.

## Constraints

- Format changes require a version-scoped compatibility case in both relevant
  directions; ordinary integration coverage is not a substitute.
- Internal representation cleanup does not justify changing the serialized
  shape when a supported older binary must still read newly written state.
- Fields and enum variants may be added, but existing positions,
  discriminants, and meanings are not repurposed.
- Monotonic persisted versions never reset or move backward.
- Wire-schema changes originate in the external protocol definition before
  the generated dependency is updated here.
- This contract applies only to declared supported ranges; it does not promise
  arbitrary downgrade compatibility.
