---
name: Account-resolution concurrency
description: The single-winner contract for concurrent account assignment of retained brokerage evidence.
---

One unresolved retained brokerage-evidence row may receive exactly one authoritative account-resolution decision. Concurrent or retried attempts after the winner commits must return a conflict rather than append another audit entry or repeat reconstruction.

**Why:** Account assignment changes reporting scope and Wheel reconstruction. A second apparent success could duplicate normalized economics or falsely imply two accounting decisions for the same immutable evidence.

**How to apply:** Keep the retained-row lock and the import audit update in the same transaction. Treat the typed account-resolution conflict as HTTP 409. Any change to this path should preserve the deterministic race test’s checks for immutable raw evidence, a single account-audit entry, no duplicate records, and one scoped rebuild.