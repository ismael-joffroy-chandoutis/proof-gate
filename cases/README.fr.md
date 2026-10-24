# Bibliothèque de cas

Chaque cas est une vraie panne silencieuse : un automatisme qui n'a rien fait, ou a fait faux, sans que rien ne le signale. Ils relèvent de trois motifs : **A**, l'outil a annoncé une réussite et on n'a vérifié que le lancement, jamais l'effet ; **B**, un contexte implicite (lien symbolique, disque monté, autorisation, variable d'environnement, planificateur) n'a jamais été testé absent ; **C**, « rien à faire » a produit exactement le même silence que « j'ai échoué ».

| N° | Cas | Motif | Outils |
|----|-----|-------|--------|
| 01 | [Une API de classement a refusé tous les envois pendant deux semaines](01-server-refusals-logged-not-raised.fr.md) | A | cron, curl, API HTTP |
| 02 | [Un outil de messagerie affichait « ENVOYÉ ET VÉRIFIÉ » pour un SMS jamais parti](02-sms-sent-and-verified-never-sent.fr.md) | A | iMessage/SMS, `chat.db`, SQLite |
| 03 | [Un flag `--print` a avalé le flag suivant et le run a rendu « réussi »](03-print-flag-swallowed-exit-zero.fr.md) | A | agy (CLI Gemini) |
| 04 | [Un clic de navigateur a répondu « clicked » et rien n'a bougé](04-click-returns-clicked-no-effect.fr.md) | A | automatisation de navigateur, JavaScript |
| 05 | [Un dossier de sauvegarde nommé « COMPLETE » était vide](05-backup-named-complete-is-empty.fr.md) | A | copie de fichiers, Photos, `du` |
| 06 | [Un verrou git périmé a bloqué tous les commits pendant 19 heures](06-stale-git-index-lock.fr.md) | A | git, `lsof` |
| 07 | [`find` ne rendait rien parce que le dossier était un lien symbolique](07-find-does-not-follow-symlink.fr.md) | B | `find`, liens symboliques |
| 08 | [La commande `at` de macOS acceptait tout et n'exécutait rien](08-at-accepts-never-runs.fr.md) | B | `at`, launchd |
| 09 | [Une autorisation disque macOS est morte en silence à une mise à jour Homebrew](09-macos-permission-dies-on-brew-upgrade.fr.md) | B | TCC, Homebrew, launchd, rsync |
| 10 | [Un essai à blanc a envoyé le message programmé pour de vrai](10-dry-run-sent-for-real.fr.md) | B | bash, `env -i` |
| 11 | [Une tâche Windows programmée « une fois » s'est lancée seule](11-schtasks-once-fires-by-itself.fr.md) | B | `schtasks`, PowerShell, GPU |
| 12 | [Gemini lit la vidéo à une image par seconde et mesurait des coupes qu'il ne voyait pas](12-gemini-video-one-frame-per-second.fr.md) | B | API Gemini, ffmpeg |
| 13 | [Des sous-appels au JSON illisible ont été écartés, et la synthèse a conclu quand même](13-unreadable-subcall-dropped-from-synthesis.fr.md) | B | Python, OpenRouter, LLM |
| 14 | [Un ménage de modèles a cassé quatre workflows ComfyUI sans un bruit](14-model-cleanup-breaks-workflows.fr.md) | B | ComfyUI, `grep` |
| 15 | [En SQLite, `strftime('%s', ...)` rend du texte, donc tout filtre de date rendait zéro](15-sqlite-strftime-returns-text.fr.md) | C | SQLite |
| 16 | [Une veille a tourné 76 fois, n'a rien produit et avait l'air active](16-output-folder-before-work.fr.md) | C | cron, `claude --print` |
| 17 | [Une boucle d'attente s'attendait elle-même](17-wait-loop-waits-for-itself.fr.md) | C | bash, `ps`, `grep` |
| 18 | [Un filtre de reprise empêchait le terminal de relancer les sessions qu'il avait lancées](18-resume-filter-blocks-resume.fr.md) | C | cmux, Claude Code, `exec` |

Version anglaise : [README.md](README.md).
