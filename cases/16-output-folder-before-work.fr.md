# Une veille a tourné 76 fois, n'a rien produit et avait l'air active

**Motif :** C : « rien à faire » et « j’ai échoué » font le même silence
**Outils :** cron, `claude --print` (Claude Code sans interface), script d'enrobage shell
**Quand :** août et septembre 2026 · **Silencieux pendant :** environ deux semaines et demie

## Ce qui était attendu
Une tâche cron toutes les 15 minutes appelait `claude --print` pour classer les messages entrants, écrire un fichier de synthèse par lot et envoyer une ligne sur mon téléphone.

## Ce qui s'est passé
La tâche a créé 76 dossiers de lot datés. Les 76 étaient vides. Aucun fichier de synthèse, aucune ligne sur le téléphone, jamais. Un message entrant qui attendait une réponse a été repris et reclassé douze fois, sans jamais m'atteindre.

## Pourquoi rien ne l'a signalé
Le script créait le dossier du lot avant d'appeler le modèle. Un dossier daté apparaissait à l'heure dite, quoi qu'il arrive ensuite. Vue de l'extérieur, l'arborescence avait exactement l'aspect d'une veille qui tourne. Le seul écart visible était l'absence de fichiers dans les dossiers, et rien ne regardait dedans. La cause était dessous : 132 passages consécutifs ont rendu « Not logged in · Please run /login ». `claude --print` lancé par cron sans `USER` exporté répond cela et sort en 1, même avec HOME et PATH corrects. La ligne de cron finissait par `>/dev/null 2>&1`, qui mangeait le message.

## Comment je l'ai découvert
En ouvrant les dossiers au lieu de les lister : 76 sur 76 vides.

## Le signal créé
`export USER` dans le script. La sonde de santé vérifie maintenant le contenu de chaque lot (les fichiers attendus existent et ne sont pas vides), jamais l'existence du dossier.

## Règle du portail
Ne jamais créer le contenant d'un résultat avant que le résultat existe ; sonder le contenu produit, jamais le réceptacle.
