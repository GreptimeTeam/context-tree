---
title: "Change-Proportional Verification"
owners: [discord9]
---

# Change-Proportional Verification

## Decision

Every behavior change carries verification at the closest useful layer, with
broader integration or SQL-level coverage when the behavior crosses crate,
protocol, storage, or deployment boundaries. Repository-wide formatting,
linting, type, and dependency-hygiene gates remain part of acceptance.

## Rationale

Local tests isolate defects cheaply, while user-visible and cross-component
tests establish that independently correct pieces still compose into the
promised database behavior. Shared quality gates keep changes maintainable
across the workspace.

## Constraints

- New or changed behavior includes the necessary unit and integration coverage
  rather than relying only on compilation.
- Public configuration changes verify loading and serialization and keep their
  examples and user-facing documentation consistent.
- Test scope follows risk: cross-protocol, distributed, storage-backend, and
  failure-sensitive behavior is exercised in the corresponding environment.
- Test-only credentials and fixtures are never represented as production-safe
  material.
