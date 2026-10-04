---
name: stories
description: Découpe une spec (ou un besoin décrit en une phrase) en stories livrables indépendamment, puis les crée comme issues GitHub après validation humaine.
argument-hint: "<docs/specs/<slug>.md | \"besoin en une phrase\">"
disable-model-invocation: true
---

# /canal:stories — découper en stories

Entrée : `$ARGUMENTS`

L'entrée est soit le chemin d'une spec, soit un besoin décrit librement (pour une petite
feature, cela donne souvent une seule story). Le modèle d'issue se trouve dans
`templates/story.md`, relatif au dossier de ce skill.

## Méthode

1. Lis la spec (ou le besoin), `AGENTS.md` et le code concerné. Vérifie aussi les issues
   `story` déjà ouvertes (`gh issue list --label story --state open`) pour éviter les
   doublons.
2. Découpe en **stories verticales** : chacune apporte une valeur visible et se teste de bout
   en bout. Une story doit tenir dans **une PR relisible**, environ une demi-journée de
   travail. Évite les stories purement techniques, sauf si c'est indispensable (une
   migration préalable, par exemple).
3. Pour chaque story, prévois un titre court à l'impératif, le besoin, des **critères
   d'acceptation testables** (Étant donné / Quand / Alors), le hors-périmètre et les
   dépendances vers d'autres stories.
4. Ordonne-les : dépendances d'abord, puis par valeur.

## Écluse

Présente le découpage sous forme de tableau (n°, titre, dépendances, résumé des critères).
Le détail complet n'est montré que sur demande. Demande à l'humain de valider, fusionner,
scinder ou retirer des stories. **Ne crée aucune issue avant l'accord.**

## Création

Pour chaque story, dans l'ordre :
```
gh issue create --title "<titre>" --body-file <fichier temporaire> --label story --label status:ready
```
Remplace les références de dépendances par les vrais numéros d'issue au fur et à mesure.
Si la spec existe, ajoute son chemin dans la section « Références ».

## Fin

Liste les issues créées avec leur lien. La prochaine étape est `/canal:plan <n>` sur la
première story sans dépendance ouverte.
