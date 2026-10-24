# Un outil de messagerie affichait « ENVOYÉ ET VÉRIFIÉ » pour un SMS jamais parti

**Motif :** A : j’ai vérifié que ça démarrait et je me suis arrêté là
**Outils :** un CLI iMessage/SMS maison, Messages de macOS, `chat.db` (SQLite)
**Quand :** octobre 2026 · **Silencieux pendant :** quelques minutes, sur ce message

## Ce qui était attendu
Mon CLI envoie un SMS depuis le portable, puis lit la base de Messages pour confirmer que le message est vraiment parti. Il n'affiche « ENVOYÉ ET VÉRIFIÉ » qu'après ce contrôle.

## Ce qui s'est passé
Le CLI a affiché « ENVOYÉ ET VÉRIFIÉ, is_sent=1 error=0 ». La vraie ligne du message dans `chat.db` valait `is_sent=0`, puis `error=4` : le relais SMS du Mac était coupé. Le message n'est jamais arrivé.

## Pourquoi rien ne l'a signalé
Le contrôle lui-même avait trois défauts. Une ligne sortante au destinataire vide passait le filtre et était prise pour mon message. La lecture était immédiate, avant que Messages écrive l'état final. La requête ne regardait que les 30 derniers messages sortants.

## Comment je l'ai découvert
J'ai lu la ligne du message dans `chat.db`, et elle contredisait le CLI.

## Le signal créé
Le destinataire doit être présent et identique à celui de l'envoi. Le contrôle lit les lignes à partir de l'heure de départ, dans l'ordre, et relit toutes les 3 secondes pendant 30 secondes. ENVOYÉ n'apparaît que si `is_sent=1` et `error=0`. Sinon l'outil rend NON CONFIRMÉ ou EN ERREUR avec le rowid, et sort avec le code 2. Testé sur trois cas réels : un échec connu, une réussite connue, un numéro sans envoi.

## Règle du portail
Un contrôle doit viser exactement l'objet qu'il prétend vérifier et attendre son état final ; une correspondance approximative est un second faux positif, pas un contrôle.
