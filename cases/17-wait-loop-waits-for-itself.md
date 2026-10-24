# A wait loop waited for itself

**Pattern:** C: "nothing to do" and "I failed" make the same silence
**Tools:** bash, `ps`, `grep`
**When:** September 2026 · **Silent for:** 25 minutes

## What was expected
A shell loop waited for a running job to finish before starting the next one: `while ps -eo command | grep -q "[v]ision_textonly"; do sleep ...; done`.

## What happened
The loop never ended. The next job never started. Twenty-five minutes went by with nothing happening.

## Why nothing flagged it
The bracket trick (`[v]ision`) stops `grep` from matching its own line. It does not protect the shell that runs the loop. That shell's command line contained the name being searched for, so the loop kept finding a match: itself. From outside, "still waiting because the job is still running" and "waiting forever because the test is wrong" look identical. Both are a quiet terminal.

## How I found out
Only by noticing, 25 minutes in, that the next job had never started.

## The signal I added
Wait on a process ID (`wait`, or `kill -0 $pid` in a loop) or on an end-of-job file that the job writes when it finishes. Never wait on a `grep` of the process list run from a shell whose own command contains the pattern.

## Gate rule
Every wait must target an identity that cannot be the waiter itself (a PID or a completion file), and must have a timeout that reports.
