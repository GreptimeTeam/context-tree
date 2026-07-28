---
title: "Live diagnostic controls"
owners: [discord9]
---

## Decision

Running nodes expose diagnostic controls that can change log verbosity and
trace collection without a restart and can capture bounded CPU and heap
profiles on demand.

## Rationale

Restarting during an incident destroys the state being investigated and can
compound an availability problem. Live controls let operators raise detail or
capture resource evidence only while the problematic behavior is present.

## Constraints

- Log filters support both a global level and module-specific overrides.
- Trace collection can be enabled or disabled independently of the configured
  log level.
- CPU profiling is time-bounded and returns an explicit analysis format.
- Heap profiling depends on a compatible allocator and build; its absence must
  not affect normal service operation.
- Increased verbosity and profiling consume resources, so incident use is
  deliberately scoped rather than treated as a permanent performance-free
  mode.
