---
title: "Protocol compatibility adapters"
owners: [discord9]
soft_links:
  - /ecosystem-adoption/protocol-compatibility-and-choice/
---

## Decision

Compatibility protocols are adapters around GreptimeDB semantics, not
independent execution engines. Each adapter owns its dialect parsing, prepared
statement behavior, type and error mapping, framing, and protocol-specific
success response while delegating database work to the frontend.

## Rationale

Existing clients require familiar wire behavior, but duplicating catalog,
authorization, or query semantics per protocol would cause incompatible
results. A narrow adapter boundary preserves client interoperability without
claiming semantic equivalence beyond the supported surface.

## Constraints

- Persisted and wire contracts evolve additively: existing fields and enum
  meanings are not repurposed or reordered.
- Generated wire types change at their owning schema before consumers adopt
  them.
- Unsupported types or features return an explicit protocol-appropriate error;
  adapters do not silently reinterpret them.
- Protocol-specific response rules may differ, but they cannot bypass common
  permission, planning, schema, or routing decisions.
