---
name: Reconciliation review schema migrations
description: Safe development-database migration approach for evolving raw import review metadata.
---

When reconciliation-review metadata evolves, add the new audit fields without dropping earlier ambiguity metadata until an explicit, reviewed data migration is planned.

**Why:** Raw import rows are audit evidence. Development databases can contain fields from an earlier review implementation even when the current application schema has moved to a newer review model; destructive schema synchronization risks losing that evidence.

**How to apply:** Prefer additive DDL for review fields during development recovery. While legacy audit values still exist, derive effective review state from either layout so unresolved evidence remains quarantined and recorded decisions remain final. Only follow an authoritative-row link when both account and source format match; then ensure the checked-in schema and its composite type declarations are refreshed before typechecking dependent services.