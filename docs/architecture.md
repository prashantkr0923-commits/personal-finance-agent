# Architecture

## Data model

Three tiers, each derived from the one before:

| Tier | What it holds | Rule |
|---|---|---|
| Raw message | Every alert and statement line exactly as received | Never edited, never deleted |
| Parsed transaction | One structured record per raw message: amount, direction, counterparty, reference, received time, transaction time, template matched | Rebuilt whenever the parser improves |
| Ledger transaction | One record per real payment after deduplication, with category and flags | Rebuilt whenever deduplication or classification rules improve |

**Why:** a mistake in parsing or classification is fixed by changing a rule and rebuilding, never by editing the ledger by hand. Every number in a report can be traced back to the raw message it came from.

A ledger view leaves pending automatic-payment notices out of every spending calculation. A notice that a payment *will* be taken is not a payment.

## Ingestion

- **Transaction alerts:** exported messages are filtered to the bank's sender and parsed with seven tested templates: UPI debit, UPI credit, mandate debit, mandate notice, bank transfer deposit, account-to-account transfer and refund. Anything no template matches is logged, never silently dropped.
- **Statements:** password-protected PDFs are read with pdfplumber. It was chosen because it opens password-protected files directly.

## Deduplication

Every UPI payment carries a unique 12-digit reference. An alert and a statement line with the same reference are the same payment and are counted once. Activity without that reference, such as card or cash payments, can't be matched this way and shows up in the gap report.

## Reconciliation

The ledger is compared with the statement for the same period. The difference is reported in two parts:

1. **UPI payments the alerts missed:** these can be fixed.
2. **Non-UPI activity:** alerts can never capture this, so it is the ceiling of an alert-based approach.

The residual gap was 1.5%, and it traced to the second kind. Reporting the ceiling honestly is a product decision: the report says what it can't see.

## Classification

- **Merchant map:** built through several review passes. Unknown merchants are exported to a CSV, reviewed, and fed back, so the map grows from real data, not guesses.
- **Recurring payments:** detected from regular intervals and amounts.
- **Fixed and variable spending:** separated, so a month's swings are judged against what can actually change.
- **Subscription audit:** active subscriptions sorted into three buckets for review.
- **Investment transfers:** recognised as transfers, not spending.

## Reports and delivery

The reports contain category breakdowns against the user's own baseline, charts, and the subscription audit. They are scheduled on the machine itself and delivered through a Telegram bot:
- attachments arrive silently, to keep notifications down;
- a failed send triggers an alert.

## Data protection

- Everything runs and is stored locally. There is no cloud deployment and no CI pipeline that could carry data off the machine.
- Only derived metadata (counts, totals, categories) may leave the data store, in logs, prompts or reports. This rule was enforced across the pipeline after a mid-build incident in which raw records were printed where they should not have been.
- Encryption at rest for the database is planned.
