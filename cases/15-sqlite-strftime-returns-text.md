# In SQLite, `strftime('%s', ...)` returns text, so every date filter returned zero

**Pattern:** C: "nothing to do" and "I failed" make the same silence
**Tools:** SQLite, macOS Messages database (`chat.db`)
**When:** September 2026 · **Silent for:** permanent until found; it had already caused one false conclusion

## What was expected
A safety check on the Messages database: `WHERE m.date/1000000000 + 978307200 > strftime('%s','now','-7 days')`. Any message in the last seven days should come back.

## What happened
Three conversations returned zero rows. The same database, queried without the filter, held 2,724, 152 and 25,127 messages for them, the latest from the previous evening. On a database of about half a million rows, `COUNT(*)` with this filter returned 0.

## Why nothing flagged it
`strftime()` returns TEXT. In SQLite's type ordering, an INTEGER is always smaller than TEXT, whatever its value. So `integer > text` is always false. No error, no warning: a valid query that structurally cannot return anything. "No message in seven days" reads like "this channel is quiet". That reading had already led to concluding that a send had not left, then sending it twice.

## How I found out
Running the same query without the date filter. Non-zero on one side, zero on the other: the filter was wrong, not the data. The same contrast caught a second defect that morning: a literal date passed to `strftime('%s', ...)` is read as UTC, which put my threshold two hours in the future.

## The signal I added
`CAST(strftime('%s','now','-7 days') AS INTEGER)` everywhere. Never a literal date in a filter, only relative ones (`'now','-N hours'`). Before concluding "empty", run the unfiltered query.

## Gate rule
A zero result from a filtered query is accepted only if the same query without the filter returns rows.
