# Roadmap

## Done (version 1)

- Ingesting transaction alerts and password-protected statements
- Seven parsing templates, with tests
- The three-tier immutable data model
- Deduplication on the UPI reference
- Reconciliation and the gap report
- Classification: merchant map, recurring payments, fixed and variable spending, subscription audit
- Scheduled reports with charts, and alerts on failure
- The evaluation harness, tested with stand-in components

## Next

1. **Natural-language query agent:** text-to-SQL over the ledger, evaluated with the harness before it is trusted.
2. **Security hardening:** encryption at rest for the database.

## Version 2: email-based

- Read transaction alert emails directly instead of exported messages.
- Send the report by email instead of through a messaging app.
- A link in the report opens one interactive page to try "what if" changes to spending categories, see projected savings, and ask questions in plain English.
- Each user would run their own copy in their own account, so nobody's financial data passes through a shared service.

The first step is checking that alert emails cover the same transactions as the exported messages before anything is rebuilt.
