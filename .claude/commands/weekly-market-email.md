---
description: Research this week's stock picks and draft a summary email
---

Run the weekly investment research pipeline end to end:

1. Invoke the `market-analyst` subagent to research and rank potential stocks to buy
   this week. It will save its report to `reports/weekly/<date>-market-analysis.md`.
2. Invoke the `email-writer` subagent, pointing it at the report just produced, to
   synthesize it into a concise email. It will save the email to
   `reports/weekly/<date>-email.md`.
3. Show the final email content back to the user and ask whether they want it created
   as an actual Gmail draft.

Run the two subagents sequentially (email-writer needs market-analyst's output), not
in parallel.
