# A watcher ran 76 times, produced nothing, and looked busy

**Pattern:** C: "nothing to do" and "I failed" make the same silence
**Tools:** cron, `claude --print` (Claude Code headless), shell wrapper
**When:** August and September 2026 · **Silent for:** about two and a half weeks

## What was expected
A cron job every 15 minutes called `claude --print` to classify incoming messages, write a summary file per batch, and send one line to my phone.

## What happened
The job created 76 dated batch folders. All 76 were empty. No summary file, no phone line, ever. An incoming message that needed an answer was picked up and reclassified twelve times, and never reached me.

## Why nothing flagged it
The wrapper created the batch folder before calling the model. A dated folder appeared on schedule whatever happened next. From outside, the directory tree looked exactly like a working watcher. The only visible gap was the absence of files inside the folders, and nothing looked inside. The cause sat underneath: 132 consecutive runs returned "Not logged in · Please run /login". `claude --print` under cron without `USER` exported answers that and exits 1, even with HOME and PATH set. The cron line ended in `>/dev/null 2>&1`, which ate the message.

## How I found out
By opening the folders instead of listing them: 76 out of 76 empty.

## The signal I added
`export USER` in the wrapper. The health probe now checks the content of each batch (the expected output files exist and are non-empty), never the existence of the folder.

## Gate rule
Never create the container of a result before the result exists; probe the content produced, never the receptacle.
