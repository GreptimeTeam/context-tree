---
title: "Recoverable table retirement"
owners: [discord9]
soft_links:
  - /architecture/storage-and-indexing/manifest-governed-object-reclamation.md
---

## Decision

When experimental soft drop and garbage collection are enabled, supported
tables move from live metadata to a retained tombstone before physical
reclamation. They may be restored during that retention window until a purge
claims the matching tombstone generation; hard drop and completed purge remain
irreversible paths.

## Rationale

Separating logical removal from physical deletion creates a bounded recovery
window for accidental drops without leaving retired storage indefinitely.
Generation-aware claims keep a stale restore or purge from acting on a newer
table incarnation.

## Constraints

- Soft drop is optional and experimental, not a universal recovery guarantee;
  unsupported table or engine forms may use hard-drop behavior.
- Restore fails when the original name is occupied or the tombstone has been
  claimed for purge.
- Retention expiry makes a tombstone eligible for automatic purge; it does not
  silently restore the table.
- Purge removes offline region state before deleting the tombstone and is
  implemented as a recoverable operation.
- Physical object deletion remains subject to the storage reclamation safety
  rules.
