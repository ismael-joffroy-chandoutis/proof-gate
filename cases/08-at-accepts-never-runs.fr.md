# La commande `at` de macOS acceptait tout et n'exécutait rien

**Motif :** B : le contexte implicite n’est jamais testé absent
**Outils :** `at`, `atq`, `atrun`, launchd, PlistBuddy
**Quand :** septembre 2026 · **Silencieux pendant :** environ sept mois

## Ce qui était attendu
Programmer une tâche ponctuelle sur le petit serveur toujours allumé avec `at`, la méthode Unix classique.

## Ce qui s'est passé
`atq` listait une tâche mise en file en février. Sept mois plus tard, elle attendait toujours. Sur macOS, `/System/Library/LaunchDaemons/com.apple.atrun.plist` porte `Disabled = true` par défaut. Le démon qui exécute les tâches `at` ne tourne pas, et rien ne le dit. La tâche oubliée contenait en plus un jeton de bot en clair, lisible par `at -c 1`.

## Pourquoi rien ne l'a signalé
`at` accepte la commande et répond « job 1 at ... ». Ce message d'acceptation est identique à une vraie programmation. Une file pleine ressemble à une file qui marche. Toute session qui programmait un rappel ou un envoi de cette façon repartait convaincue que c'était armé.

## Comment je l'ai découvert
En vérifiant qu'aucun envoi programmé n'était armé avant une échéance du matin, j'ai listé la file et vu une tâche de février.

## Le signal créé
Une règle : rien ne se programme avec `at` sur cette machine. Les tâches différées passent par launchd ou par le motif `nohup sleep N; script &` gardé par un drapeau d'idempotence. Le seul contrôle qui tranche : `/usr/libexec/PlistBuddy -c "Print :Disabled" /System/Library/LaunchDaemons/com.apple.atrun.plist`.

## Règle du portail
Un planificateur se prouve par une tâche qu'il a réellement exécutée, pas par son message d'acceptation ; tester l'exécuteur avant de croire la file.
