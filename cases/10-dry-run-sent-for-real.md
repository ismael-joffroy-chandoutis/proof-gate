# A dry run sent the scheduled message for real

**Pattern:** B: the implicit context is never tested absent
**Tools:** bash, `env -i`, a scheduled send script, a messaging app
**When:** October 2026 · **Silent for:** none; the damage was immediate

## What was expected
A message was armed to leave at a set time in the morning. Before leaving it alone, I tested the script in dry-run mode: `DRY=1 env -i HOME=... PATH=... bash send.sh`. The script was supposed to see `DRY=1`, check everything and send nothing.

## What happened
The message left in the middle of the night, hours early. `env -i` empties the environment after the `DRY=1` assignment has been applied to `env` itself. The variable never reached the script, which saw no dry-run flag and sent.

## Why nothing flagged it
The dry-run switch lived in an environment variable, an implicit context the script never tested as absent. The script had no way to tell "no flag was given" from "the flag got lost on the way". It was a repeat: earlier the same month, another dry run had sent for real because its fake flag sat on an empty path.

## How I found out
The message was delivered. That was the first signal.

## The signal I added
A dry-run mode never goes through an environment variable. It goes through an argument (`send.sh --dry`) tested at the top of the script, which exits before any send command. Or a fake flag file, verified present before launching. When the send command is real, the first test is `bash -n` and reading the script, without executing it.

## Gate rule
The safe mode must be the default; a script that can reach a human should send only when an explicit argument says so.
