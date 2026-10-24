# Des sous-appels au JSON illisible ont été écartés, et la synthèse a conclu quand même

**Motif :** B : le contexte implicite n’est jamais testé absent
**Outils :** Python, OpenRouter, sous-appels LLM qui rendent du JSON, un appel de synthèse
**Quand :** septembre 2026 · **Silencieux pendant :** un run de nuit par modèle

## Ce qui était attendu
Un script d'analyse de documents découpait un long dossier en sous-appels de huit pages chacun, envoyés par OpenRouter. Chaque sous-appel rendait du JSON. Un appel de synthèse final lisait toutes les parties et écrivait les conclusions.

## Ce qui s'est passé
Tout sous-appel dont la réponse n'était pas du JSON valide (chaîne tronquée, contenu `None`) n'a jamais été vu par la synthèse. Avec un modèle, 13 sous-appels sur 24 perdus : trois blocs sur six privés de groupes de pages entiers. Avec un autre modèle, 3 perdus. Trois des quatre constats faux du premier modèle (« il n'y a pas de X dans le dossier ») venaient directement des pages manquantes.

## Pourquoi rien ne l'a signalé
Le script écrivait « JSON illisible partie N » dans le journal du run et continuait. La synthèse rendait un JSON complet. Le bloc était marqué produit. Seule une ligne de « limites » laissait deviner le trou. L'hypothèse implicite, que toutes les parties avaient répondu, n'était jamais testée.

## Comment je l'ai découvert
Un modèle évaluateur a compté les pages que la synthèse ne citait jamais. Un `grep -c "JSON illisible"` sur les journaux l'a confirmé : 13 et 3.

## Le signal créé
Dans la version suivante, tout sous-appel sans JSON valide échoue bruyamment et se relance. La synthèse reçoit la liste des pages réellement vues et refuse de conclure sur une pièce qu'elle n'a pas vue.

## Règle du portail
Dans tout traitement découpé puis fusionné, compter les parties attendues et les parties reçues avant la fusion ; un écart fait échouer le run, ce n'est pas une note de bas de page.
