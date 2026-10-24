# Une tâche Windows programmée « une fois » s'est lancée seule

**Motif :** B : le contexte implicite n’est jamais testé absent
**Outils :** Planificateur de tâches Windows (`schtasks`), PowerShell, un entraînement de LoRA sur une tour GPU
**Quand :** octobre 2026 · **Silencieux pendant :** environ trois heures

## Ce qui était attendu
Sur une tour GPU Windows, j'ai créé une tâche avec `/SC ONCE /ST 23:59` dans le seul but d'avoir un nom à lancer à la main par `schtasks /Run`. L'heure était un remplissage.

## Ce qui s'est passé
Le remplissage était un vrai déclencheur. À 23:59, la tâche est partie seule. Elle a rouvert un entraînement de LoRA déjà terminé à 4 000 pas, n'a rien appris, a réécrit le fichier final, puis s'est bloquée en tenant la carte graphique près de trois heures. Un autre travail sur la même carte a fortement ralenti : une image a pris 8 616 secondes au lieu de 12.

## Pourquoi rien ne l'a signalé
Une tâche planifiée qui tourne, c'est le comportement normal du planificateur. Rien ne distinguait un lancement voulu d'un lancement accidentel. Le processus d'entraînement ressemblait à n'importe quel travail GPU.

## Comment je l'ai découvert
Seulement en préparant le travail suivant sur cette tour. Rien ne me l'avait dit avant.

## Le signal créé
Toute tâche destinée au lancement manuel est maintenant datée dans un futur lointain (`/SD 01/01/2030 /ST 00:00`) et supprimée après usage. Avant tout lancement sur une tour, je liste `Get-ScheduledTask | ? State -eq Running` et les processus Python avec leur date de création. Un processus d'entraînement dont l'heure de départ ne correspond à aucun lancement de ma part est un incident.

## Règle du portail
Avant de lancer un travail sur une machine partagée, lister ce qui tourne déjà et depuis quand ; tout ce qu'on ne sait pas expliquer est une panne à régler d'abord.
