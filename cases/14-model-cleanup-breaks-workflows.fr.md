# Un ménage de modèles a cassé quatre workflows ComfyUI sans un bruit

**Motif :** B : le contexte implicite n’est jamais testé absent
**Outils :** ComfyUI, fichiers de modèles (`.safetensors`), `grep`, une machine GPU partagée
**Quand :** octobre 2026 · **Silencieux pendant :** environ une heure

## Ce qui était attendu
Libérer de l'espace sur une machine GPU utilisée par quelqu'un d'autre, en supprimant des modèles que j'avais listés comme « remplacés » ou « anciens » : une variante d'encodeur de texte, un modèle d'édition d'image avec sa LoRA et son encodeur. La liste a été validée avant suppression.

## Ce qui s'est passé
Ces fichiers étaient encore référencés par quatre workflows ComfyUI (versions brouillon, finale et API) installés sur la machine deux semaines plus tôt, avec une notice. Après le ménage, les workflows étaient cassés.

## Pourquoi rien ne l'a signalé
Un workflow ComfyUI qui pointe vers un fichier absent ne prévient personne avant qu'on l'ouvre et qu'on le lance. Ma liste disait ce qu'était chaque fichier, pas ce qui l'utilisait. L'hypothèse implicite, que rien ne dépendait d'un fichier « ancien », n'a jamais été testée.

## Comment je l'ai découvert
Par hasard, une heure plus tard, en installant un nouveau workflow dans le même dossier.

## Le signal créé
Les fichiers ont été retéléchargés depuis les listes de téléchargement gardées sur la machine, un encodeur est passé en version int8, les workflows modifiés ont été sauvegardés, et les workflows réparés ont été testés en file. Avant toute suppression de modèle désormais : `grep -rl <nom du fichier>` dans tous les dossiers de workflows de la machine, et une colonne « utilisé par » dans le tableau soumis à validation.

## Règle du portail
Avant de supprimer un fichier, chercher tout ce qui le référence ; « ancien » est une supposition, un compte de références est un fait.
