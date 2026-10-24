# En SQLite, `strftime('%s', ...)` rend du texte, donc tout filtre de date rendait zéro

**Motif :** C : « rien à faire » et « j’ai échoué » font le même silence
**Outils :** SQLite, base de Messages de macOS (`chat.db`)
**Quand :** septembre 2026 · **Silencieux pendant :** en permanence jusqu'à la découverte ; il avait déjà causé une fausse conclusion

## Ce qui était attendu
Un contrôle de sécurité sur la base de Messages : `WHERE m.date/1000000000 + 978307200 > strftime('%s','now','-7 days')`. Tout message des sept derniers jours devait remonter.

## Ce qui s'est passé
Trois conversations rendaient zéro ligne. La même base, interrogée sans le filtre, contenait 2 724, 152 et 25 127 messages pour elles, le plus récent de la veille au soir. Sur une base d'environ un demi-million de lignes, `COUNT(*)` avec ce filtre rendait 0.

## Pourquoi rien ne l'a signalé
`strftime()` rend du TEXT. Dans l'ordre des types de SQLite, un INTEGER est toujours inférieur à du TEXT, quelle que soit sa valeur. Donc `entier > texte` vaut toujours faux. Aucune erreur, aucun avertissement : une requête valide qui ne peut structurellement rien rendre. « Aucun message en sept jours » se lit comme « ce canal est calme ». Cette lecture avait déjà fait conclure qu'un envoi n'était pas parti, puis l'envoyer deux fois.

## Comment je l'ai découvert
En lançant la même requête sans le filtre de date. Non nul d'un côté, zéro de l'autre : le filtre était faux, pas les données. Le même contraste a attrapé un second défaut le même matin : une date littérale passée à `strftime('%s', ...)` est lue en UTC, ce qui plaçait mon seuil deux heures dans le futur.

## Le signal créé
`CAST(strftime('%s','now','-7 days') AS INTEGER)` partout. Jamais de date littérale dans un filtre, seulement des dates relatives (`'now','-N hours'`). Avant de conclure au vide, lancer la requête sans filtre.

## Règle du portail
Un résultat nul d'une requête filtrée n'est accepté que si la même requête sans filtre rend des lignes.
