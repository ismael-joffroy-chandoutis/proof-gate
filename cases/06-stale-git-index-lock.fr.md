# Un verrou git périmé a bloqué tous les commits pendant 19 heures

**Motif :** A : j’ai vérifié que ça démarrait et je me suis arrêté là
**Outils :** git, `lsof`, plusieurs sessions d'agents sur un même dépôt
**Quand :** août et septembre 2026 · **Silencieux pendant :** 6 heures la première fois, 19 heures la seconde

## Ce qui était attendu
Plusieurs sessions d'agents committent leur travail dans le même dépôt git au fil de la journée.

## Ce qui s'est passé
En septembre, deux minutes après un commit fait juste avant de débrancher le disque externe, un processus git s'est arrêté en laissant `.git/index.lock` (0 octet). Aucun commit n'est passé pendant 19 heures. En août, même chose : six heures, 699 fichiers non versionnés empilés sur trois sessions.

## Pourquoi rien ne l'a signalé
Une session qui écrit sans committer ne voit rien. Celle qui committe reçoit « index.lock: File exists » et peut croire qu'un autre git travaille. En août, le commit était en bout de chaîne `cmd && cmd && git commit` et l'erreur se perdait dans le flot. Personne ne vérifiait qu'un commit avait produit un nouveau SHA. `lsof` montrait en plus le verrou ouvert par un outil sans rapport, ce qui ressemblait à un détenteur légitime. Tenir un descripteur ne veut pas dire posséder le verrou.

## Comment je l'ai découvert
Un commit a été refusé. J'ai comparé l'heure du verrou à celle du dernier commit : deux minutes d'écart, des heures d'âge.

## Le signal créé
Diagnostic : `lsof -c git` ne montre aucun processus git, le fichier fait 0 octet et date de plusieurs heures, il est donc mort. Le sauvegarder, puis le retirer. Une sonde signale désormais tout `index.lock` de plus d'une heure dans les dépôts connus.

## Règle du portail
Un commit est fait quand `HEAD` porte un nouveau SHA ; comparer le SHA avant et après, jamais le fait que `git commit` ait été lancé.
