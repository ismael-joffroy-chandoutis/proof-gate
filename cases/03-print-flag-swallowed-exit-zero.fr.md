# Un flag `--print` a avalé le flag suivant et le run a rendu « réussi »

**Motif :** A : j’ai vérifié que ça démarrait et je me suis arrêté là
**Outils :** agy (CLI agent Gemini), tâches shell en arrière-plan
**Quand :** septembre 2026 · **Silencieux pendant :** quelques minutes, rattrapé tout de suite

## Ce qui était attendu
Une recherche déléguée à un CLI agent Gemini : `agy --print --effort low ... < brief.md`. Le brief demandait un rapport écrit à un chemin absolu imposé.

## Ce qui s'est passé
Le processus est sorti au bout de trois minutes avec le code 0. La tâche de fond s'est annoncée « completed ». Aucun rapport n'a été écrit. `--print` prend un argument. Sans `=`, il a pris `--effort` comme prompt et ignoré le brief passé sur l'entrée standard.

## Pourquoi rien ne l'a signalé
Le message d'erreur existait, mais dans la sortie, pas dans le code de retour. La notification de fin était identique à celle d'une recherche réussie.

## Comment je l'ai découvert
J'ai contrôlé le fichier de sortie au chemin absolu imposé par le brief, dans la minute qui a suivi la notification. Zéro fichier, donc échec, quel que soit le code.

## Le signal créé
La forme qui marche passe le brief comme valeur du flag : `--print="$(cat brief.md)"`, avec `--print-timeout 25m`, car le défaut de 5 minutes est trop court pour une recherche. Vérifié le soir même : un rapport de 22 Ko en six minutes. Le signal est le fichier de sortie, jamais le code de retour.

## Règle du portail
Une tâche déléguée est faite quand son fichier de sortie attendu existe et a du contenu ; un code 0 prouve seulement que le processus s'est terminé.
