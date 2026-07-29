---
title: "Experimental Surface Gating"
owners: [discord9]
---

# Experimental Surface Gating

## Decision

Features whose behavior or public surface may still change ship behind
configuration explicitly prefixed `experimental_`. They enter the stable
surface only when their behavior is ready to become a compatibility commitment.

## Rationale

An ordinary configuration key looks durable to operators. Explicit gating lets
unfinished work be exercised without accidentally freezing its semantics into
the long-term support contract.

## Constraints

- Unstable functionality is not exposed through an unmarked stable
  configuration surface.
- Per-object overrides preserve the same experimental designation.
- Stabilization removes the experimental designation and documents the
  operator-facing migration.
- Changes to experimental behavior remain reviewable and tested; the prefix is
  not a waiver of quality or safety obligations.
