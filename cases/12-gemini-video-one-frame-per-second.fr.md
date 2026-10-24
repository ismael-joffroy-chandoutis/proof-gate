# Gemini lit la vidéo à une image par seconde et mesurait des coupes qu'il ne voyait pas

**Motif :** B : le contexte implicite n’est jamais testé absent
**Outils :** API Gemini (entrée vidéo), `ffmpeg`
**Quand :** septembre 2026 · **Silencieux pendant :** rattrapé avant usage des chiffres

## Ce qui était attendu
Une analyse de montage d'un corpus de films, confiée à Gemini : durée moyenne des plans, plan le plus court, J-cuts et L-cuts au timecode.

## Ce qui s'est passé
L'API Gemini échantillonne la vidéo à 1 image par seconde. Tout plan de moins d'une seconde lui est invisible. Deux coupes rapprochées se confondent. Mes J-cuts, à 0,45 seconde selon ma propre règle de mixage, n'existent pas dans ce que le modèle reçoit. Sur un corpus dont la distribution des durées de plans culmine sous 2 secondes, le modèle aurait rendu des chiffres cohérents, assurés et faux.

## Pourquoi rien ne l'a signalé
Le modèle « voit » le fichier, j'en ai donc supposé qu'il voyait chaque image. Rien dans sa réponse ne parle de l'échantillonnage. Le contexte implicite (la résolution temporelle de l'entrée) n'a jamais été vérifié.

## Comment je l'ai découvert
Une question, posée avant le lancement : le modèle reçoit-il la vidéo à 24 images par seconde ? Non.

## Le signal créé
La mesure et l'interprétation sont séparées. `ffmpeg` mesure : `select='gt(scene,N)',showinfo` rend les coupes à l'image près. Mon œil calibre le seuil sur une planche contact. Le modèle interprète ensuite, sur des chiffres justes. Avant toute analyse média confiée à un modèle, je me demande : à quelle cadence reçoit-il le fichier, et cette cadence permet-elle la mesure demandée ?

## Règle du portail
Ne jamais accepter une mesure d'un modèle sans connaître la résolution de ce qu'il a réellement reçu ; mesurer avec un outil déterministe, interpréter avec le modèle.
