---
name: Stock allocation chronology
description: Conservation and ordering rules for assigned lots, dispositions, and covered-call reservations.
---

Stock dispositions and covered-call reservations/releases must mutate stock inventory in one stable chronological stream. Future calls or later coverage releases cannot change whether an earlier stock sale was supported.

**Why:** Processing all calls before all stock sales lets future events reserve or release shares retroactively, corrupting campaign membership, remaining exposure, and realized stock P&L.

**How to apply:** Exclude lots not yet acquired; conserve quantity, cash, fees, basis, and assigned premium with decrementing integer remainders; pair same-day call-away stock cash without disposing shares twice. Keep lot P&L based on disposition price and lot basis, while reporting source-ledger cash separately.