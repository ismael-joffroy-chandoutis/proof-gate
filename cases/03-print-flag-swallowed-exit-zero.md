# A `--print` flag swallowed the next flag and the run reported success

**Pattern:** A: I checked that it started and stopped there
**Tools:** agy (Gemini agent CLI), shell background jobs
**When:** September 2026 · **Silent for:** a few minutes, caught right away

## What was expected
A research task delegated to a Gemini agent CLI: `agy --print --effort low ... < brief.md`. The brief asked for a report written to a fixed absolute path.

## What happened
The process exited after three minutes with code 0. The background job announced "completed". No report was written. `--print` takes an argument. Without `=`, it took `--effort` as the prompt and ignored the brief passed on standard input.

## Why nothing flagged it
The error message existed, but inside the output, not in the exit code. The completion notice was identical to the one a successful research run would produce.

## How I found out
I checked the output file at the absolute path the brief imposed, within a minute of the notification. Zero files means failure, whatever the exit code.

## The signal I added
The working form passes the brief as the flag's value: `--print="$(cat brief.md)"`, plus `--print-timeout 25m`, because the default of 5 minutes is too short for research. Verified the same evening: a 22 KB report in six minutes. The signal is the output file, never the return code.

## Gate rule
A delegated job is done when its expected output file exists and has content; an exit code of 0 proves only that the process ended.
