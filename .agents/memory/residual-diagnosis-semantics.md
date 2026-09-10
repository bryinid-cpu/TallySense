---
name: Residual diagnosis semantics
description: Rules for classifying residual quantities and deriving report completeness without false authority.
---

Treat the quantity on a residual edge as unresolved quantity, with allocated quantity zero unless the edge explicitly records a partial allocation. Assign reason codes and resolution classes at the branch that creates the residual; do not infer them from display text.

**Why:** Generic string mapping and treating residual quantity as allocated caused unknown economics to appear known and made unrelated report dimensions look complete.

**How to apply:** Derive each completeness dimension from its own typed impact flag. Attribution-only residuals must not block ledger or stock-basis reporting, and share-denominated residuals must not automatically block stock basis.