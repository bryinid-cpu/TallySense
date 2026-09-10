---
name: Multi-fill reconstruction
description: Safety rules for grouping brokerage executions into directional trades and Wheel legs.
---

Compatible fills may aggregate only when account, option contract, orientation, lifecycle state, and quantity reconciliation identify one logical position. Same-day source ordering can be corrected only when later compatible opening fills exactly satisfy an otherwise oversubscribed close; broader same-day overlap remains unresolved.

**Why:** Robinhood activity data is day-granular and may list closes before related opening fills, but it has no execution IDs or intraday timestamps to support broad relationship guessing.

**How to apply:** Preserve every execution event. If an outcome exceeds remaining contracts, retain it as campaign evidence and mark review rather than truncating it. Reserve stock shares for all outstanding covered-call legs so multiple calls cannot silently claim the same shares. Allocate only the consumed opening-fragment premium proportionally to assigned shares; never transfer BTC costs or assignment-event cash into stock basis.