# Une autorisation disque macOS est morte en silence à une mise à jour Homebrew

**Motif :** B : le contexte implicite n’est jamais testé absent
**Outils :** TCC de macOS (accès complet au disque), `python3` de Homebrew, launchd, `rsync`, SQLite
**Quand :** septembre 2026 · **Silencieux pendant :** plusieurs jours

## Ce qui était attendu
Un LaunchAgent lançait le `python3` de Homebrew toutes les 3 minutes pour lire la base de Messages (`chat.db`) et surveiller les nouveaux messages.

## Ce qui s'est passé
Chaque passage écrivait « chat.db inaccessible (OperationalError), mails seuls ». Les messages n'étaient surveillés par personne. Mon diagnostic, noté puis répété le lendemain : « accorder l'accès complet au disque à python3 ». Faux : l'accès était accordé. Mais macOS attache l'autorisation au chemin versionné du binaire dans le Cellar de Homebrew. L'accès existait pour trois anciennes versions de Python. Un `brew upgrade` en avait installé une nouvelle, et l'autorisation pointait en silence vers des binaires disparus.

## Pourquoi rien ne l'a signalé
La ligne d'erreur revenait toutes les 3 minutes, 574 passages par jour. Un message aussi fréquent devient du décor. Pire, le même code lisait `chat.db` sans problème depuis une session Claude Code interactive, dont le processus détenait l'autorisation en propre.

## Comment je l'ai découvert
Ma conclusion a été contestée : « l'accès est accordé ». En relisant les entrées TCC, les chemins versionnés sont apparus.

## Le signal créé
`rsync` détient l'autorisation en propre quand launchd le lance directement. Un nouvel agent recopie `chat.db`, `-wal` et `-shm` toutes les 3 minutes, et la surveillance lit la copie. Contrôlé par `PRAGMA quick_check`, même nombre de lignes, même `MAX(ROWID)`. Passer par `/bin/bash` ne marchait pas. La fraîcheur se lit dans le `--log-file` de rsync, car `rsync -a` garde les anciennes dates.

## Règle du portail
Ne jamais lier l'autorisation d'un automatisme à un chemin de binaire versionné ; après toute mise à jour, relancer l'automatisme et lire sa vraie sortie.
