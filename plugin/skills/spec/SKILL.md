---
name: spec
description: Rédige une spécification fonctionnelle par interview de l'humain et exploration du code, dans docs/specs/<slug>.md. Étape de cadrage assistée du workflow canal.
argument-hint: "<sujet ou besoin en quelques mots>"
disable-model-invocation: true
---

# /canal:spec — cadrer un besoin

Sujet : `$ARGUMENTS`

Tu produis une spec fonctionnelle claire et bornée, qui servira à découper des stories.
Cette commande est **assistée** : c'est l'humain qui connaît le besoin, toi tu poses les
bonnes questions et tu structures. Le modèle se trouve dans `templates/spec.md`, relatif au
dossier de ce skill.

## Méthode

1. **Contexte** : lis `AGENTS.md`, regarde les specs existantes dans `docs/specs/` et
   explore le code lié au sujet. Tu dois connaître l'existant avant de poser des questions.
2. **Interview** : pose tes questions **par petits groupes (3 au maximum)**, dans cet ordre,
   et seulement celles dont la réponse n'est pas déjà évidente :
   - le problème à résoudre et pour qui ;
   - le résultat attendu, et comment on saura que c'est réussi ;
   - le périmètre : ce qui est inclus et, surtout, **ce qui est exclu** ;
   - les règles métier, les cas limites, les droits d'accès ;
   - les contraintes (techniques, délais, données existantes).
   Propose des réponses par défaut quand c'est possible, pour que l'humain n'ait qu'à
   confirmer.
3. **Rédaction** : remplis le modèle. Écris des exigences testables, sans détail
   d'implémentation (c'est le rôle du plan). Signale ce qui reste flou dans « Questions
   ouvertes ».
4. **Écluse** : présente la spec et demande validation. Itère jusqu'à l'accord. Passe alors
   le frontmatter à `status: validated`.

## Fin

Écris `docs/specs/<slug>.md`, puis propose de la committer. Prochaine étape :
`/canal:stories docs/specs/<slug>.md`.
