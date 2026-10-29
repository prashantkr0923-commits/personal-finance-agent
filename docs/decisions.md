# Decisions

## D1. Personal use only, on one machine

**Options:** a product for many users; a single-user tool (chosen).

**Why:** a personal tool avoids the regulatory and platform hurdles a public finance product faces, such as data-protection compliance, app-store rules on reading messages, and verification for email access. That keeps the build focused on whether the ledger is right.

## D2. SQLite, not a server database

**Why:** one user and a low transaction volume. A database server would add setup and moving parts with no benefit. Encryption at rest is planned.

## D3. Deterministic parsing first, AI only for the long tail

**Options:** send every message to a language model; hand-written templates for known formats, with the model as a fallback (chosen).

**Why:** bank alerts follow a small number of fixed formats. Templates are exact, free, fast and testable, and a model would add cost and occasional confident mistakes to the part that is already solved. The model is kept for what templates can't do: unknown formats, merchant enrichment and natural-language questions.

## D4. An immutable three-tier data model

**Why:** the ledger can be rebuilt from raw input at any time, so every fix is a rule change, not a hand edit. This paid off when investment transfers were found being counted as spending. The rule was fixed and the whole history rebuilt correctly.

## D5. Deduplicate on the payment reference

**Why:** the same payment appears in the alert and the statement. The 12-digit UPI reference is unique, which makes matching exact rather than fuzzy on amount and date.

## D6. Report the gap instead of hiding it

**Why:** alerts can't capture non-UPI activity. Rather than claim full coverage, the reconciliation reports the residual gap (1.5%) and what causes it, so the user knows what the report can and can't see.

## D7. Received time and transaction time are separate fields

**Why:** an automatic debit can be notified on one day and posted on another. Using only the received time put some payments in the wrong period.

## D8. Build the evaluation before the query agent

**Why:** a text-to-SQL agent can return an answer that reads as correct but isn't, and that can't be caught by looking at it. A query that returns plausible but wrong numbers is worse than one that fails loudly. So the evaluation harness came before the agent.

## D9. Only derived metadata leaves the data store

**Why:** during the build, a debugging step printed raw records where it should not have. The fix was a pipeline-wide rule, not a one-off patch: logs, prompts and reports may carry counts, totals and categories, never raw records.

## D10. Version 2 will be email-based, not a rewrite of version 1

**Options:** keep extending the local version; build an email-based version 2 alongside it (chosen).

**Why:** reading transaction alert emails and sending the report by email removes the manual export step and the messaging app. It also opens a path for other people to run their own copy in their own account. The local version 1 stays as it is.
