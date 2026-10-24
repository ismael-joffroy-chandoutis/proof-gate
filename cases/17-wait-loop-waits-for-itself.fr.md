# Une boucle d'attente s'attendait elle-même

**Motif :** C : « rien à faire » et « j’ai échoué » font le même silence
**Outils :** bash, `ps`, `grep`
**Quand :** septembre 2026 · **Silencieux pendant :** 25 minutes

## Ce qui était attendu
Une boucle shell attendait la fin d'un travail en cours avant de lancer le suivant : `while ps -eo command | grep -q "[v]ision_textonly"; do sleep ...; done`.

## Ce qui s'est passé
La boucle ne s'est jamais terminée. Le travail suivant n'a jamais démarré. Vingt-cinq minutes sont passées sans que rien n'arrive.

## Pourquoi rien ne l'a signalé
L'astuce des crochets (`[v]ision`) empêche `grep` de se trouver lui-même. Elle ne protège pas le shell qui porte la boucle. La ligne de commande de ce shell contenait le nom cherché, donc la boucle trouvait toujours une correspondance : elle-même. Vu de l'extérieur, « j'attends encore parce que le travail tourne » et « j'attends pour toujours parce que le test est faux » sont identiques. Dans les deux cas, un terminal calme.

## Comment je l'ai découvert
Seulement en remarquant, au bout de 25 minutes, que le travail suivant n'avait jamais démarré.

## Le signal créé
Attendre sur un identifiant de processus (`wait`, ou `kill -0 $pid` en boucle) ou sur un fichier de fin que le travail écrit en se terminant. Jamais sur un `grep` de la liste des processus lancé depuis un shell dont la propre commande contient le motif.

## Règle du portail
Toute attente doit viser une identité qui ne peut pas être l'attendant lui-même (un PID ou un fichier de fin), avec un délai maximal qui se signale.
