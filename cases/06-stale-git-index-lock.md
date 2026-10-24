# A stale git lock blocked every commit for 19 hours

**Pattern:** A: I checked that it started and stopped there
**Tools:** git, `lsof`, several agent sessions sharing one repository
**When:** August and September 2026 · **Silent for:** 6 hours the first time, 19 hours the second

## What was expected
Several agent sessions commit their work into the same git repository through the day.

## What happened
In September, two minutes after a commit made just before I unplugged the external disk, a git process stopped and left `.git/index.lock` (0 bytes) behind. No commit went through for 19 hours. In August the same thing had happened: six hours, 699 untracked files piled up across three sessions.

## Why nothing flagged it
A session that writes files without committing sees nothing. A session that commits gets "index.lock: File exists" and may assume another git is working. In August the commit sat at the end of a `cmd && cmd && git commit` chain and the error got lost in the flow. Nobody checked that a commit had produced a new SHA. `lsof` also showed the lock open by an unrelated tool, which looked like a legitimate holder. Holding a descriptor does not mean owning the lock.

## How I found out
A commit was refused. I compared the lock's timestamp with the last commit: two minutes apart, many hours old.

## The signal I added
Diagnosis: `lsof -c git` shows no git process, the file is 0 bytes and hours old, so it is dead. Back it up, then remove it. A watcher now reports any `index.lock` older than one hour in known repositories.

## Gate rule
A commit is done when `HEAD` has a new SHA; check the SHA before and after, never the fact that `git commit` ran.
