# Un filtre de reprise empêchait le terminal de relancer les sessions qu'il avait lancées

**Motif :** C : « rien à faire » et « j’ai échoué » font le même silence
**Outils :** cmux (terminal pour sessions d'agents), Claude Code, un script d'enrobage avec `exec`, `ps`
**Quand :** octobre 2026 · **Silencieux pendant :** environ neuf jours

## Ce qui était attendu
Après un crash, mon terminal relance les sessions d'agents qui tournaient. Un petit script filtre se plaçait devant chaque relance pour décider quelles sessions reprendre, et journalisait chaque décision.

## Ce qui s'est passé
Après un crash, 9 sessions sont reparties sur 40 actives. Le journal du filtre ne contenait pas une ligne pour ce jour-là. Un crash précédent avait rendu 9 sessions sur 27, probablement pour la même raison.

## Pourquoi rien ne l'a signalé
Le terminal ne relance que les onglets marqués `wasAgentRunning`, et il ne marque « en cours » qu'un processus nommé `claude`. Mon filtre faisait `exec` sur le vrai binaire, nommé `claude.exe`. Toute session lancée par le filtre était donc vue arrêtée, même vivante, donc jamais relancée, donc le filtre jamais appelé. Un journal vide se lisait comme « rien à reprendre ».

## Comment je l'ai découvert
En comptant les sessions après le crash : 9 reparties sur 40, et aucune ligne de journal pour expliquer le reste.

## Le signal créé
Le filtre lance maintenant le lien symbolique `claude` qui pointe vers le binaire, donc le processus s'appelle `claude`. Testé : une session lancée ainsi est marquée `wasAgentRunning` vrai. Contrôles à garder : zéro processus nommé `claude.exe` dans les onglets du terminal, et après un crash, aucune ligne du jour dans le journal du filtre veut dire panne, pas « rien à faire ».

## Règle du portail
Un composant qui journalise ses décisions doit signaler quand il n'est jamais appelé ; après l'événement pour lequel il existe, un journal vide est une panne.
