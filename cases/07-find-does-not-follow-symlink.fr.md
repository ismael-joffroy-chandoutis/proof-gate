# `find` ne rendait rien parce que le dossier était un lien symbolique

**Motif :** B : le contexte implicite n’est jamais testé absent
**Outils :** `find`, liens symboliques, fichiers de session Claude Code (`.jsonl`)
**Quand :** septembre 2026 · **Silencieux pendant :** au moins huit heures

## Ce qui était attendu
Une boucle de surveillance listait à chaque cycle les autres sessions d'agents actives : `find <dossier projets> -name '*.jsonl' -mmin -40`. Tout fichier de session modifié dans les 40 dernières minutes signalait une session vivante.

## Ce qui s'est passé
Chaque cycle concluait « aucune autre session active ». Pendant ce temps, environ neuf sessions et un workflow automatique tournaient. Les messages envoyés par ces sessions n'arrivaient jamais au registre des envois que la boucle tenait.

## Pourquoi rien ne l'a signalé
Un mois plus tôt, le dossier des projets était devenu un lien symbolique vers le disque externe. Sans `-L`, `find` ne descend pas dans un lien symbolique passé comme point de départ. Sortie vide, code 0, aucun message. Un résultat vide se lit exactement comme « rien à signaler ». Le piège était même écrit dans mes propres notes. Le lire n'a pas suffi.

## Comment je l'ai découvert
Les envois faits par d'autres sessions pendant ces heures n'apparaissaient jamais dans le registre que le balayage devait alimenter.

## Le signal créé
`find -L`, ou le chemin réel via `readlink`, sur tout dossier qui peut être un lien symbolique. Et un contrôle positif : le balayage doit au moins compter la session courante, toujours active. Zéro résultat plusieurs cycles de suite signifie que la commande est fausse, pas que le monde est calme.

## Règle du portail
Toute recherche doit inclure dans son périmètre un positif connu ; si elle ne trouve pas ce qu'on sait exister, son « rien trouvé » ne vaut rien.
