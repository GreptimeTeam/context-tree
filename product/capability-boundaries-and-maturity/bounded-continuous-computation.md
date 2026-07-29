---
title: "Bounded Continuous Computation"
owners: [discord9]
---

# Bounded Continuous Computation

## Decision

Continuous computation is a database-adjacent capability for maintaining
aggregated or materialized results over time-series data. It is deliberately
bounded and does not position GreptimeDB as a general-purpose stream
processing platform.

## Rationale

Downsampling and continuously refreshed aggregates are natural extensions of
an observability database. A focused capability serves those workloads
without importing the product scope and operational complexity of a complete
streaming system.

## Constraints

- Time windows and materialized results are the organizing use cases.
- Only documented continuous-query behavior is part of the product contract;
  arbitrary streaming operators are not implied.
- The capability remains optional to deployments that need only storage and
  query.
