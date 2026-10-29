# Evaluation

## Why it comes first

The query agent will answer questions such as "how much did I spend on food last month?" by writing SQL against the ledger. The dangerous failure is not an error message. It is a number that looks right and isn't. That can't be caught by reading the answer, so the evaluation harness was built before the agent.

## What the harness checks

| Part | How it works | Why |
|---|---|---|
| Golden set | Questions with verified answers, each tagged by the type of question it asks | Accuracy is measured per slice, not just overall |
| SQL correctness | The agent's query and the reference query are both run against a frozen copy of the data, and their results compared | Two different queries can both be right, and one query can look right and be wrong. Comparing results catches both |
| Numbers in the answer | Every number in the written answer must match a number in the query result, checked by code | A judge model shouldn't be trusted with arithmetic |
| Answer quality | Judge prompts with written rubrics score clarity and whether the answer addresses the question | Some qualities can't be checked by code |
| Judge calibration | Judge scores compared with human labels on a sample, reported as Cohen's kappa | Raw agreement looks good by chance; kappa corrects for that |
| Disposition | Whether the agent answers, asks for clarification or declines, scored as a confusion matrix | A wrong answer costs more than an unnecessary clarifying question, so the two error directions are weighted differently |
| Regression gate | A release is blocked if any slice gets worse, even when the overall score improves | An overall gain can hide a slice that broke |

## Status

The harness is built and was tested end to end with stand-in components. That run caught and fixed one bug in the harness itself: a check wrongly penalised the agent for declining when the right answer was "uncategorised". It will be connected to the query agent as the agent is built, with the golden set using synthetic data so it can be shared.
