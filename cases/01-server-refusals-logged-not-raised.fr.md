# Une API de classement a refusé tous les envois pendant deux semaines

**Motif :** A : j’ai vérifié que ça démarrait et je me suis arrêté là
**Outils :** cron, scripts shell, curl, API d'un classement public d'usage
**Quand :** octobre 2026 · **Silencieux pendant :** environ deux semaines

## Ce qui était attendu
Trois scripts planifiés sur le petit serveur toujours allumé envoyaient chaque jour ma consommation de tokens à un classement public. Chaque passage devait mettre mes totaux à jour.

## Ce qui s'est passé
Le serveur a commencé à refuser les envois non signés pour les comptes vérifiés (« need to be signed »). Les scripts ont reçu ce refus 187 fois. Un second défaut s'ajoutait : le serveur exigeait aussi un bloc `totals` à côté de `daily`, et mon convertisseur n'envoyait que `daily`. Deux autres sources d'usage ont été refusées 90 fois chacune en une seule nuit. Mon rang a glissé pendant que les scripts « tournaient ».

## Pourquoi rien ne l'a signalé
Les scripts partaient à l'heure et sortaient normalement. Le refus était écrit dans un journal de maintenance et nulle part ailleurs. Une ligne de journal que personne ne lit n'est pas un signal.

## Comment je l'ai découvert
En lisant le journal lui-même. Rien ne m'a remonté l'échec.

## Le signal créé
Un jeton d'API rangé dans un fichier de configuration local, lu par les trois scripts. Si le jeton manque, le script sort avec le code 3 et écrit une ligne au journal. Les requêtes portent un en-tête `Authorization: Bearer`, et le convertisseur écrit `totals`. Reste à brancher : tout envoi qui reçoit autre chose que `"success":true` après son dernier essai envoie une notification.

## Règle du portail
Un envoi n'est fait que si le corps de la réponse dit qu'il a réussi ; toute autre réponse finale doit atteindre un humain, pas seulement un journal.
