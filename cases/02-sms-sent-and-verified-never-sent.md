# A messaging CLI printed "SENT AND VERIFIED" for an SMS that never left

**Pattern:** A: I checked that it started and stopped there
**Tools:** a homemade iMessage/SMS CLI, macOS Messages, `chat.db` (SQLite)
**When:** October 2026 · **Silent for:** minutes, on that message

## What was expected
My CLI sends an SMS from the laptop, then reads the Messages database to confirm the message really left. It prints "SENT AND VERIFIED" only after that check.

## What happened
The CLI printed "SENT AND VERIFIED, is_sent=1 error=0". The real row for that message in `chat.db` said `is_sent=0`, then `error=4`: the Mac's SMS relay was cut. The message never reached the recipient.

## Why nothing flagged it
The verification itself had three defects. An outgoing row with an empty handle passed the filter and was taken for my message. The read happened immediately, before Messages wrote the final state. The query only looked at the 30 most recent outgoing rows.

## How I found out
I read the message's own row in `chat.db` and it contradicted the CLI.

## The signal I added
The handle must be present and identical to the recipient. The check reads rows from the send's start time, in order, and re-reads every 3 seconds for 30 seconds. SENT is printed only if `is_sent=1` and `error=0`. Otherwise the tool prints NOT CONFIRMED or ERROR with the rowid and exits with code 2. I tested it on three real cases: a known failure, a known success, and a number with no send.

## Gate rule
A verification step must match the exact object it claims to verify and wait for its final state; a loose match is a second false positive, not a check.
