# A macOS disk permission died silently at a Homebrew upgrade

**Pattern:** B: the implicit context is never tested absent
**Tools:** macOS TCC (Full Disk Access), Homebrew `python3`, launchd, `rsync`, SQLite
**When:** September 2026 · **Silent for:** several days

## What was expected
A LaunchAgent ran Homebrew's `python3` every 3 minutes to read the Messages database (`chat.db`) and watch for new messages.

## What happened
Every pass logged "chat.db inaccessible (OperationalError), mail only". Messages were watched by nobody. My diagnosis, written down and repeated the next day, was "grant Full Disk Access to python3". Wrong: access was granted. But macOS attaches the permission to the versioned binary path in Homebrew's Cellar. Access existed for three older Python versions. A `brew upgrade` had installed a new one, and the permission silently pointed to binaries that no longer existed.

## Why nothing flagged it
The error line repeated every 3 minutes, 574 passes a day. A message that frequent becomes background noise. Worse, the same code read `chat.db` fine from an interactive Claude Code session, whose own process held the permission.

## How I found out
My conclusion was challenged: "the access is granted". Rechecking the TCC entries showed the versioned paths.

## The signal I added
`rsync` holds the permission in its own right when launchd runs it directly. A new agent mirrors `chat.db`, `-wal` and `-shm` every 3 minutes, and the watcher reads the mirror. Checked with `PRAGMA quick_check`, same row count, same `MAX(ROWID)`. Going through `/bin/bash` did not work. Freshness is read from the rsync `--log-file`, because `rsync -a` keeps old timestamps.

## Gate rule
Never bind an automation's permission to a versioned binary path; after any upgrade, re-run the automation and read its real output.
