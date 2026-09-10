---
name: Campaign link review
description: Scope and safety boundary for trader-driven Wheel Campaign ownership corrections.
---

Campaign-link review is a separate layer after source reconciliation and logical-leg reconstruction. It handles valid events or legs whose Wheel Campaign ownership is ambiguous; it does not replace raw-record reconciliation or multi-fill grouping.

**Why:** Duplicate source records and same-contract execution aggregation have different evidence, decisions, and failure modes from deciding which valid economic event belongs to which campaign.

**How to apply:** Preserve raw evidence, constrain candidate campaigns to the same account and economically compatible ticker/quantity/coverage, support assign/unassign/decide-later decisions, and audit corrections separately before recalculating only the affected scope.

Campaign-link assignments must target a stable campaign identity, not the transient database campaign ID created by a rebuild.

**Why:** Wheel reconstruction replaces campaign rows, so a decision tied only to an old numeric ID can silently point nowhere or to the wrong freshly rebuilt campaign.

**How to apply:** Persist the stable opening-event campaign key with the audit decision; accept the current campaign ID only as a guarded UI selection aid, then validate and resolve it back to that stable key during rebuild.