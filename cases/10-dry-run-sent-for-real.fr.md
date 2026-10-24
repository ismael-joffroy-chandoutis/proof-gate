# Un essai à blanc a envoyé le message programmé pour de vrai

**Motif :** B : le contexte implicite n’est jamais testé absent
**Outils :** bash, `env -i`, un script d'envoi programmé, une application de messagerie
**Quand :** octobre 2026 · **Silencieux pendant :** aucun délai ; le dégât a été immédiat

## Ce qui était attendu
Un message était armé pour partir à une heure fixée, le matin. Avant de le laisser seul, j'ai testé le script à blanc : `DRY=1 env -i HOME=... PATH=... bash send.sh`. Le script devait voir `DRY=1`, tout contrôler et ne rien envoyer.

## Ce qui s'est passé
Le message est parti en pleine nuit, des heures trop tôt. `env -i` vide l'environnement après que l'affectation `DRY=1` a été appliquée à `env` lui-même. La variable n'est jamais arrivée au script, qui n'a vu aucun drapeau d'essai et a envoyé.

## Pourquoi rien ne l'a signalé
L'interrupteur d'essai vivait dans une variable d'environnement, un contexte implicite que le script ne testait jamais absent. Il ne pouvait pas distinguer « aucun drapeau donné » de « drapeau perdu en route ». C'était une récidive : plus tôt dans le mois, un autre essai à blanc avait envoyé pour de vrai parce que son faux drapeau était posé sur un chemin vide.

## Comment je l'ai découvert
Le message a été reçu. C'était le premier signal.

## Le signal créé
Un mode à blanc ne passe jamais par une variable d'environnement. Il passe par un argument (`send.sh --dry`) testé en tête de script, qui sort avant toute commande d'envoi. Ou par un faux drapeau, vérifié présent avant de lancer. Quand la commande d'envoi est réelle, le premier essai est `bash -n` et la lecture du script, sans l'exécuter.

## Règle du portail
Le mode sûr doit être le mode par défaut ; un script qui peut atteindre un humain ne doit envoyer que sur un argument explicite.
