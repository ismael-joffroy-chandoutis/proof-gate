# Case library

Each case is a real silent failure: an automation that did nothing, or did the wrong thing, while nothing reported it. They fall into three patterns: **A**, the tool reported success and only the launch was checked, never the effect; **B**, an implicit context (a symlink, a mounted disk, a permission, an environment variable, a scheduler) was never tested as absent; **C**, "nothing to do" produced exactly the same silence as "I failed".

| # | Case | Pattern | Tools |
|---|------|---------|-------|
| 01 | [A leaderboard API refused every upload for two weeks](01-server-refusals-logged-not-raised.md) | A | cron, curl, HTTP API |
| 02 | [A messaging CLI printed "SENT AND VERIFIED" for an SMS that never left](02-sms-sent-and-verified-never-sent.md) | A | iMessage/SMS, `chat.db`, SQLite |
| 03 | [A `--print` flag swallowed the next flag and the run reported success](03-print-flag-swallowed-exit-zero.md) | A | agy (Gemini CLI) |
| 04 | [A browser click returned "clicked" and nothing changed](04-click-returns-clicked-no-effect.md) | A | browser automation, JavaScript |
| 05 | [A backup folder named "COMPLETE" was empty](05-backup-named-complete-is-empty.md) | A | file copy, Photos, `du` |
| 06 | [A stale git lock blocked every commit for 19 hours](06-stale-git-index-lock.md) | A | git, `lsof` |
| 07 | [`find` returned nothing because the directory was a symlink](07-find-does-not-follow-symlink.md) | B | `find`, symlinks |
| 08 | [macOS `at` accepted every job and never ran any](08-at-accepts-never-runs.md) | B | `at`, launchd |
| 09 | [A macOS disk permission died silently at a Homebrew upgrade](09-macos-permission-dies-on-brew-upgrade.md) | B | TCC, Homebrew, launchd, rsync |
| 10 | [A dry run sent the scheduled message for real](10-dry-run-sent-for-real.md) | B | bash, `env -i` |
| 11 | [A Windows task scheduled "once" fired by itself](11-schtasks-once-fires-by-itself.md) | B | `schtasks`, PowerShell, GPU |
| 12 | [Gemini reads video at one frame per second and measured cuts it could not see](12-gemini-video-one-frame-per-second.md) | B | Gemini API, ffmpeg |
| 13 | [Sub-calls with unreadable JSON were dropped, and the synthesis concluded anyway](13-unreadable-subcall-dropped-from-synthesis.md) | B | Python, OpenRouter, LLM |
| 14 | [A model cleanup broke four ComfyUI workflows without a sound](14-model-cleanup-breaks-workflows.md) | B | ComfyUI, `grep` |
| 15 | [In SQLite, `strftime('%s', ...)` returns text, so every date filter returned zero](15-sqlite-strftime-returns-text.md) | C | SQLite |
| 16 | [A watcher ran 76 times, produced nothing, and looked busy](16-output-folder-before-work.md) | C | cron, `claude --print` |
| 17 | [A wait loop waited for itself](17-wait-loop-waits-for-itself.md) | C | bash, `ps`, `grep` |
| 18 | [A session-resume filter stopped the terminal from resuming the sessions it launched](18-resume-filter-blocks-resume.md) | C | cmux, Claude Code, `exec` |

French versions: [README.fr.md](README.fr.md).
