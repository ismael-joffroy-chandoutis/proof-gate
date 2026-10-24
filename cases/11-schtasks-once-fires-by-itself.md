# A Windows task scheduled "once" fired by itself

**Pattern:** B: the implicit context is never tested absent
**Tools:** Windows Task Scheduler (`schtasks`), PowerShell, a LoRA training job on a GPU tower
**When:** October 2026 · **Silent for:** about three hours

## What was expected
On a Windows GPU tower, I created a task with `/SC ONCE /ST 23:59` only to have a named handle that I would start by hand with `schtasks /Run`. The time was a placeholder.

## What happened
The placeholder was a real trigger. At 23:59 the task started on its own. It reopened a LoRA training that had already finished at 4,000 steps, learned nothing, overwrote the final file, then hung while holding the GPU for close to three hours. Another job on the same card slowed down badly: one image took 8,616 seconds instead of 12.

## Why nothing flagged it
A scheduled task that runs is normal behaviour for the scheduler. Nothing distinguished a wanted run from an accidental one. The training process looked like any other GPU job.

## How I found out
Only while preparing the next job on that tower. Nothing had told me before.

## The signal I added
Every task meant for manual launch is now dated in the far future (`/SD 01/01/2030 /ST 00:00`) and deleted after use. Before any launch on a tower, I list `Get-ScheduledTask | ? State -eq Running` and the Python processes with their creation time. A training process whose start time matches no launch I made is an incident.

## Gate rule
Before starting work on a shared machine, list what is already running and when it started; anything you cannot account for is a failure to resolve first.
