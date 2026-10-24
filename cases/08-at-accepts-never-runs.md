# macOS `at` accepted every job and never ran any

**Pattern:** B: the implicit context is never tested absent
**Tools:** `at`, `atq`, `atrun`, launchd, PlistBuddy
**When:** September 2026 · **Silent for:** about seven months

## What was expected
Scheduling a one-off job on the always-on server with `at`, the classic Unix way.

## What happened
`atq` listed a job queued in February. Seven months later it was still waiting. On macOS, `/System/Library/LaunchDaemons/com.apple.atrun.plist` carries `Disabled = true` by default. The daemon that executes `at` jobs does not run, and nothing says so. The forgotten job also held a bot token in clear text, readable by anyone with `at -c 1`.

## Why nothing flagged it
`at` accepts the command and answers "job 1 at ...". That acceptance message is identical to a real scheduling. A full queue looks like a working queue. Any session that scheduled a reminder or a send this way would leave convinced it was armed.

## How I found out
While checking that no scheduled send was armed before a morning deadline, I listed the queue and saw a job from February.

## The signal I added
A rule: nothing is scheduled with `at` on that machine. Scheduled work goes through launchd or a `nohup sleep N; script &` pattern guarded by an idempotence flag. The one check that settles the question: `/usr/libexec/PlistBuddy -c "Print :Disabled" /System/Library/LaunchDaemons/com.apple.atrun.plist`.

## Gate rule
A scheduler is proven by one job it actually executed, not by its acceptance message; test the executor before trusting the queue.
