# A leaderboard API refused every upload for two weeks

**Pattern:** A: I checked that it started and stopped there
**Tools:** cron, shell scripts, curl, a public usage leaderboard API
**When:** October 2026 · **Silent for:** about two weeks

## What was expected
Three scheduled scripts on the small always-on server uploaded my daily token usage to a public leaderboard. Each run was supposed to update my totals.

## What happened
The server started refusing unsigned uploads for verified accounts ("need to be signed"). The scripts received that refusal 187 times. A second defect stacked on top: the server also required a `totals` block next to `daily`, and my converter only sent `daily`. Two other usage sources were refused 90 times each in a single night. My rank slid while the scripts kept "running".

## Why nothing flagged it
The scripts ran on schedule and exited normally. The refusal was written to a maintenance log and nowhere else. A log line that nobody reads is not a signal.

## How I found out
By reading the log itself. Nothing pushed the failure to me.

## The signal I added
An API token stored in a local config file, read by all three scripts. If the token is missing, the script exits with code 3 and writes a log line. Requests now carry an `Authorization: Bearer` header, and the converter writes `totals`. The rule still to wire: any upload that gets anything other than `"success":true` after its last retry sends a notification.

## Gate rule
An upload is done only when the response body says it succeeded; any other final answer must reach a human, not just a log.
