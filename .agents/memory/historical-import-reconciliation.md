---
name: Historical import reconciliation
description: Durable rules for source identities, overlapping brokerage reports, and account-safe Wheel reconstruction.
---

Broker-issued transaction IDs and deterministic fallback identities have different reconciliation guarantees. A correction may update a record only when it has a broker transaction identity. Any repeated fallback identity—whether its visible fingerprint matches or differs—must retain the existing ledger record and mark the incoming occurrence as ambiguous for review.

**Why:** Broker exports can reorder or overlap, lack IDs, and contain genuinely distinct fills with identical visible fields. Treating a fallback collision as an automatic duplicate or update silently corrupts historical evidence.

**How to apply:** Build reconciliation keys from source format, account identity, and source identity; protect them with a database uniqueness guarantee and make import writes conflict-safe. Carry account identity on every normalized record. Mixed-account source files must preserve each row's account and be marked for review rather than inheriting the first account. Wheel reconstruction must group and link activity by account plus ticker, and take its advisory lock before reading the event snapshot.

Records with no reliable account identity are retained as `unscoped` evidence but must be quarantined from automatic Wheel campaign, option-leg, and stock-lot reconstruction until a user assigns them.

**Why:** Treating the placeholder scope as an actual account can silently combine same-symbol activity belonging to different brokerage accounts.

**How to apply:** Mark missing-account imports reviewable, expose their per-account/review breakdown in import history, and never let `unscoped` events enter lifecycle linking or any default/account-specific analytics. Only an explicit account resolution may make them reportable.

Account resolution is an explicit, server-validated reassignment from quarantined source rows to an already detected account. It must preserve source evidence, re-scope reconciliation keys, refresh import audit coverage, and rebuild account-scoped lifecycle state atomically.

**Why:** A UI-only account picker, a fabricated target account, or a partial reassignment/rebuild would silently corrupt the account boundary or leave Wheel state stale.

**How to apply:** Accept only rows still tied to unscoped normalized records; reject stale retries and target accounts absent from imported activity. Perform source-row status updates, normalized account/reconciliation updates, audit updates, and Wheel reconstruction in one transaction.

An ambiguous row without an assigned account must complete account review before it can enter reconciliation review. Reconciliation targets must be recomputed from every retained normalized identity, scoped to the selected account, and exclude records from the same raw row. A correction is permitted only for one normalized record with no companion activity.

**Why:** Account identity determines the valid conflict set. Reusing stored conflict references, or resolving only one part of a multi-record source row, can select the wrong ledger record or silently omit accounting activity.

**How to apply:** Keep unscoped collisions in account-assignment review. After assignment, preserve the pending normalized evidence and create a reconciliation-review item rather than auto-inserting it. Require explicit accounting confirmation, record that confirmation with the decision, and rebuild the affected account after a successful decision.

An explicit duplicate decision authorizes only a byte-for-byte equivalent fallback replay, identified by the account-scoped source identity and canonical normalized record values; changed evidence sharing that identity must remain reviewable.

**Why:** Fallback identities can repeat across overlapping brokerage exports, but a matching identity alone does not prove a changed fill or summary is the same activity. Conversely, replay recognition must work for both retained-evidence and legacy audit layouts without reopening a settled decision.

**How to apply:** Build replay authorization keys from the stored source identity plus canonical dates and accounting fields, and recover the same key from preserved normalized rows—following a validated same-account/source authoritative raw-row link when older audit data lacks retained JSON evidence.
