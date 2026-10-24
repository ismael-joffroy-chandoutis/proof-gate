# A session-resume filter stopped the terminal from resuming the sessions it launched

**Pattern:** C: "nothing to do" and "I failed" make the same silence
**Tools:** cmux (terminal for agent sessions), Claude Code, a shell wrapper using `exec`, `ps`
**When:** October 2026 · **Silent for:** about nine days

## What was expected
After a crash, my terminal app relaunches the agent sessions that were running. A small filter script sat in front of each relaunch to decide which sessions to resume, and logged every decision.

## What happened
After a crash, 9 sessions came back out of 40 that were active. The filter's log had not a single line for that day. An earlier crash had brought back 9 out of 27, probably for the same reason.

## Why nothing flagged it
The terminal only relaunches tabs flagged `wasAgentRunning`, and it flags as running only a process named `claude`. My filter did `exec` on the real binary, named `claude.exe`. Every session launched through the filter was therefore seen as stopped, even while alive, so it was never relaunched, so the filter was never called. An empty log read as "nothing to resume".

## How I found out
Counting sessions after the crash: 9 back out of 40, and no log line to explain the rest.

## The signal I added
The filter now launches the `claude` symlink that points to the binary, so the process name is `claude`. Tested: a session started this way shows `wasAgentRunning` true. Checks to keep: zero processes named `claude.exe` in the terminal's tabs, and after a crash, no log line of the day in the filter means failure, not "nothing to do".

## Gate rule
A component that logs its decisions must log when it is never called; after the event it exists for, an empty log is a failure.
