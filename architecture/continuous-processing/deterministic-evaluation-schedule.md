---
title: "Deterministic Evaluation Schedule"
owners: [discord9]
---

# Deterministic Evaluation Schedule

## Decision

Interval-based flows resolve their schedule once into durable typed state,
including its anchor, start point, and missed-tick policy. Recovery resumes
from that schedule rather than deriving a new cadence from the restart wall
clock. Each execution carries its scheduled logical time through planning and
distributed execution.

## Rationale

A durable schedule prevents restarts, retries, or node placement from shifting
window boundaries or changing the meaning of time-dependent expressions. A
single logical evaluation time also keeps every region in one flow run
semantically aligned.

## Constraints

- Evaluation intervals are positive.
- Missed work follows an explicit bounded catch-up or skip policy; recovery
  does not replay an unbounded backlog implicitly.
- Time-dependent expressions use the scheduled logical instant, not whichever
  node's wall clock happens to plan or execute the subquery.
- Schedule defaults are resolved before distribution and stored with the flow
  definition so every component receives the same contract.
