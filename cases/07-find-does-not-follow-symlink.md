# `find` returned nothing because the directory was a symlink

**Pattern:** B: the implicit context is never tested absent
**Tools:** `find`, symlinks, Claude Code session files (`.jsonl`)
**When:** September 2026 · **Silent for:** at least eight hours

## What was expected
A monitoring loop listed the other active agent sessions every cycle: `find <projects dir> -name '*.jsonl' -mmin -40`. Any session file modified in the last 40 minutes meant a live session.

## What happened
Every cycle concluded "no other active session". Meanwhile about nine sessions and one automated workflow were running. Messages sent by those sessions never reached the send register that the loop maintained.

## Why nothing flagged it
A month earlier the projects directory had become a symlink to the external disk. Without `-L`, `find` does not descend into a symlink passed as its starting point. Output empty, exit code 0, no message. An empty result reads exactly like "nothing to report". The pitfall was even written in my own notes. Reading it was not enough.

## How I found out
Sends made by other sessions during those hours never appeared in the register the sweep was supposed to feed.

## The signal I added
`find -L`, or resolve the real path with `readlink`, on any directory that can be a symlink. And a positive control: the sweep must at least count the current session, which is always active. Zero results for several cycles in a row means the command is wrong, not that the world is quiet.

## Gate rule
Every search must include a known positive in its scope; if it cannot find the thing you know exists, its "nothing found" is void.
