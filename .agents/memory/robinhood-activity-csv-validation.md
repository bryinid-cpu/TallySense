---
name: Robinhood activity CSV validation
description: Constraints observed in the first real Robinhood activity CSV and matching account statements.
---

The Robinhood activity CSV is the primary import candidate, but it must be parsed as RFC4180 CSV because quoted descriptions can span lines. It supplies activity, process, and settlement dates; transaction codes; net amounts; and option identity embedded in descriptions, but no file-level brokerage account identifier or stable activity/execution/order identifier.

**Why:** Line-oriented parsing breaks valid multiline rows, while account inference or fallback-only deduplication can mix accounts or collapse legitimate identical fills.

**How to apply:** Keep every valid source row as evidence. Parse option descriptions only for the supported `TICKER M/D/YYYY Put|Call $STRIKE` and expiration variants; preserve unrecognized cases for review. Quarantine accountless activity until the importer receives an explicit account confirmation. Treat repeated fallback fingerprints as review items, never automatic deduplication.

Option assignment is represented as an `OASGN` row followed by an associated stock `Buy` (put assignment) or `Sell` (call assignment) row. Link only when account, activity date, ticker, strike, and the 100-shares-per-contract quantity relationship agree; flag non-unique candidates.

**Why:** The source does carry enough evidence for assignment-aware option/stock reconstruction, but it does not carry an explicit link ID.

**How to apply:** Use the CSV net amount as the economic cash flow, classify ACH, transfers, interest, margin interest, Gold activity, stock lending, and miscellaneous adjustments as retained non-trading account activity, and retain PDFs as account-identified reconciliation references rather than import sources.

Some live exports append the Robinhood disclaimer as a ten-cell footer (nine blank cells and disclaimer text) and leave `Amount` blank for `OEXP` and `OASGN` lifecycle rows.

**Why:** Rejecting either pattern discards an otherwise valid export or breaks expiration/assignment reconstruction, even though those lifecycle events legitimately have no cash flow on their own.

**How to apply:** Ignore only the recognized disclaimer footer after header validation. Require a numeric amount for cash-bearing codes, but normalize supported expiration and assignment codes with blank amounts as zero-cash lifecycle events.

Retain the exact text and original logical ordinal of every post-header CSV record, including blank, footer, and malformed records. Non-transaction evidence inherits the file's explicit account scope but must never become a normalized event.

**Why:** Re-serializing parsed fields loses original quoting and line endings, while dropping non-transaction records breaks byte-level evidence coverage and can shift later source ordinals.

**How to apply:** Keep classification separate from normalization, and verify that retained logical-record text and ordinals cover every post-header source record without gaps or reformatting.