<picture><source media="(prefers-color-scheme: dark)" srcset="banner.svg"><img src="banner-light.svg" alt="Proof gate" width="100%"></picture>

[English](README.md) · [Français](README.fr.md)

# Proof gate

En octobre 2026, un script de mon petit serveur toujours allumé envoyait chaque matin mes données d'usage à un classement en ligne. Pendant deux semaines, le serveur a refusé chaque envoi, 187 fois, parce que le compte exigeait désormais des requêtes signées. Le script a écrit chaque refus dans un journal que je n'ouvre jamais, a réessayé, et s'est terminé proprement. Chaque passage s'est terminé sans erreur, et rien n'est arrivé sur le classement. Je l'ai découvert en regardant le classement.

<img src="monde.jpg" alt="image-monde" width="100%">

Je travaille seul avec une flotte d'agents IA (Claude Code, Codex, Gemini CLI), des tâches planifiées et des scripts répartis sur un portable, un petit serveur et trois tours GPU. Depuis août 2026, je tiens un registre de chaque incident où quelque chose n'a pas eu lieu sans que rien ne le dise : plus de cent entrées en trois mois. Ce dépôt rassemble la méthode qui en est sortie et une sélection de ces cas, anonymisés.

## La règle

Une tâche est faite quand un signal frais, observé sur l'effet réel, le dit. Un code de sortie 0, un message « success », une tâche planifiée chargée ou un fichier présent prouvent seulement que quelque chose a démarré. La preuve se lit sur l'effet : le message dans le dossier des envoyés, la ligne sur le serveur, la taille et l'empreinte de la sauvegarde, l'image à l'écran, relus après l'action.

## Trois motifs

| | Motif | Forme typique |
|---|---|---|
| A | J’ai vérifié que ça démarrait et je me suis arrêté là | Un outil répond « envoyé », « cliqué », « success » ; rien n'a eu lieu |
| B | Le contexte implicite n'est jamais testé absent | Un lien symbolique vers un disque démonté, le PATH de cron, une autorisation macOS liée à un ancien binaire, le compte qui se trouve connecté |
| C | « Rien à faire » et « j'ai échoué » font le même silence | Une veille qui a tourné 76 fois sans rien produire, une boucle qui s'attend elle-même, un journal vide qui a l'air calme |

Une question pour tout automatisme : **s'il ne fait rien, qu'est-ce que je vois ?** Si la réponse est « rien », c'est une panne silencieuse en attente, et la corriger consiste à créer le signal.

## Les règles du portail

Ce sont les contrôles que le portail applique avant que quoi que ce soit soit déclaré fait.

1. **La preuve avant la relecture.** Chaque « fait » porte un signal observé, pris après l'action : une sortie, une ligne sur le serveur, une taille et une empreinte, une capture.
2. **Le risque règle la friction.** Un changement est classé selon ce qu'il touche. Risque faible : la preuve suffit. Risque courant : il me demande. Risque critique : il me pose une question sur ce qui change avant que je puisse approuver, parce qu'un développeur seul qui approuve son propre travail tape « oui » par réflexe.
3. **Le relecteur vient d'une autre famille.** Ce qu'un modèle dans le cloud a écrit est vérifié par d'autres modèles, ou par un outil déterministe.
4. **Les boucles sont bornées.** Deux agents qui défont le travail l'un de l'autre sont arrêtés et le problème me revient.
5. **Les règles se gardent elles-mêmes.** Toute modification des instructions des agents, des prompts ou de la configuration du portail passe au risque maximal.
6. **Chaque incident devient un contrôle, et chaque contrôle parle.** Un contrôle qui ne trouve rien écrit une ligne datée qui le dit ; une alerte qui reste dans un fichier journal compte comme une absence d'alerte.

## Les cas

[`cases/`](cases/README.fr.md) contient les incidents anonymisés, un fichier par cas en anglais et en français : ce qui était attendu, ce qui s'est passé, pourquoi rien ne l'a signalé, comment je l'ai découvert, le signal ajouté, et la règle du portail qui en est sortie.

## État

La méthode et les cas servent dès maintenant, à la lecture. Un portail en ligne de commande (crochets git et contrôles après action, avec des modèles locaux) est en cours de spécification.

---

État vérifié au 24 octobre 2026.

Ismaël Joffroy Chandoutis, 2026. Textes sous licence [CC BY-SA 4.0](LICENSE).

Par [Ismaël Joffroy Chandoutis](https://ismaeljoffroychandoutis.com).
