# Un clic de navigateur a répondu « clicked » et rien n'a bougé

**Motif :** A : j’ai vérifié que ça démarrait et je me suis arrêté là
**Outils :** un navigateur intégré piloté par un agent (`eval` JavaScript, `click`, captures de page), une visionneuse de documents web
**Quand :** septembre 2026 · **Silencieux pendant :** une boucle de fond et trois tentatives

## Ce qui était attendu
Un agent pilotait une visionneuse de documents web pour cliquer sur « Next document » et lire le fichier suivant d'une série de trois.

## Ce qui s'est passé
La visionneuse est restée sur « Document 1/3 ». Une boucle de fond a échoué, puis trois tentatives de plus, de la même façon. Au lieu d'avancer, la visionneuse ouvrait une modale : « You must read the entire document ». Sauter à la dernière page depuis la liste des pages ne comptait pas non plus comme lecture, et rien ne le disait.

## Pourquoi rien ne l'a signalé
Le clic JavaScript rendait « clicked ». Le clic de l'outil navigateur rendait OK. La modale n'apparaissait que dans la capture complète de la page, pas dans la capture « éléments interactifs » que lisait l'agent.

## Comment je l'ai découvert
J'ai relu l'intitulé « Document N/3 » après chaque clic. Il n'avait pas changé.

## Le signal créé
La séquence qui marche fait défiler le conteneur du document jusqu'en bas par `eval` (`scrollTop = scrollHeight`, puis un événement `scroll`), puis clique. Plus généralement : après toute action de navigateur censée changer l'état, relire l'état (intitulé, URL, texte visible) et le comparer à l'état d'avant. Un état inchangé est un échec, quel que soit le retour du clic.

## Règle du portail
Une action d'interface est faite quand l'état qu'elle devait changer a changé ; la valeur de retour du clic n'est pas une preuve.
