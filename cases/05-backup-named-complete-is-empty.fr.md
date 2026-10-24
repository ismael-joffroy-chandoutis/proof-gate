# Un dossier de sauvegarde nommé « COMPLETE » était vide

**Motif :** A : j’ai vérifié que ça démarrait et je me suis arrêté là
**Outils :** copie de fichiers vers un disque externe, photothèque Photos de macOS, `du`, `find`
**Quand :** septembre 2026 · **Silencieux pendant :** environ six semaines

## Ce qui était attendu
Une copie complète de mon disque de données principal, photothèque Photos comprise, sur un disque de sauvegarde branché au petit serveur toujours allumé. Le nom du dossier finissait par `COMPLETE` suivi de la date.

## Ce qui s'est passé
Dans ce dossier, la photothèque pesait 0 octet, contenait 0 fichier dans `originals` et une base illisible. La copie lancée en août avait échoué et laissé une coquille vide. Une copie voisine était réelle (777 Go, 326 876 originaux) mais ne contenait aucune vidéo : zéro `.mov`, zéro `.mp4`.

## Pourquoi rien ne l'a signalé
La copie a échoué sans rien dire. Le dossier existait, au bon endroit, avec un nom qui affirmait l'inverse de son contenu. Le nom tenait lieu de preuve.

## Comment je l'ai découvert
J'allais répondre « oui » à la question : si j'efface la photothèque du disque principal, j'en ai bien une copie ailleurs ? Répondre sur la foi du nom détruisait 372 798 éléments. Compter les fichiers d'abord a montré la réalité.

## Le signal créé
Une sauvegarde se contrôle par `du -sm`, un compte de fichiers par type et l'ouverture d'un échantillon. Jamais par son nom, jamais par le code de retour de la copie. La question à se poser : si la copie avait entièrement échoué, qu'est-ce que je verrais ? Ici : un dossier bien nommé, bien rangé, vide.

## Règle du portail
Avant toute étape destructive qui s'appuie sur une sauvegarde, mesurer son contenu (taille, nombre de fichiers par type, un échantillon ouvert) ; son nom et son journal de copie ne prouvent rien.
