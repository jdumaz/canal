---
name: fix
description: Petite correction sans plan (texte, style, bug trivial et localisé), avec un périmètre strictement limité, une revue légère et une PR. Bascule vers le flux story si le changement dépasse le cadre.
argument-hint: "\"<description de la correction>\""
disable-model-invocation: true
---

# /canal:fix — correction rapide

Demande : `$ARGUMENTS`

C'est l'**unique exception** à la règle « aucun code sans plan validé ». Elle est
**assistée** : l'humain voit le diff avant le commit.

## Garde-fou de périmètre

Le changement est éligible seulement s'il remplit **toutes** ces conditions :
- 3 fichiers au plus et une cinquantaine de lignes environ ;
- aucune migration, aucun changement de schéma, de dépendance, de route publique, de
  permission ou de configuration sensible ;
- aucune règle métier nouvelle ou modifiée ;
- l'intention est sans ambiguïté.

Si ce n'est pas le cas (dès l'analyse ou en cours de route), arrête-toi et propose
`/canal:stories "<besoin>"`.

## Déroulé

1. Lis `AGENTS.md`. Pars de la branche principale à jour et crée `fix/<slug>`. Le working
   tree doit être propre.
2. Fais la modification toi-même, dans le style du code environnant. Ajoute ou adapte un
   test si le comportement change.
3. Lance les commandes de vérification d'`AGENTS.md` et corrige jusqu'à ce qu'elles passent.
4. Lance l'agent `canal:reviewer` sur le diff, en précisant qu'il s'agit d'un fix (il n'y a
   ni plan ni issue : la description ci-dessus tient lieu de critère). Corrige les findings
   BLOQUANT et IMPORTANT. Sans sous-agents, relis toi-même selon `../../agents/reviewer.md`.
5. **Écluse** : montre le diff (`git diff`) et le résultat de la revue. Attends l'accord.
6. Commit en Conventional Commits (`fix: …`, `style: …`, `docs: …`), push, puis
   `gh pr create`.
7. Demande à l'humain s'il veut merger maintenant. Si oui, applique la même règle que
   `/canal:ship` : vérifie la CI, puis `gh pr merge --squash --delete-branch`, et reviens
   sur la branche principale à jour.
