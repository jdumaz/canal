---
name: ship
description: Présente une synthèse de la PR d'une story (changements, revue, CI) et, sur décision explicite de l'humain, la merge en squash ou renvoie ses retours vers un nouveau build.
argument-hint: "<numéro d'issue>"
disable-model-invocation: true
---

# /canal:ship — décider du merge

Issue : `$ARGUMENTS`

C'est l'écluse finale. **Tu ne merges jamais sans un accord explicite donné dans cette
session.**

## 1. Synthèse

Retrouve la PR (`gh pr list --search "<n> in:body" --state open`, ou à partir de la branche
`story/<n>-*`). Si elle n'existe pas, arrête-toi et propose `/canal:build <n>`.

Présente de façon compacte :
- **Ce qui change** : 3 à 6 puces fonctionnelles, et les fichiers principaux
  (`gh pr diff --name-only`) ;
- **Critères d'acceptation** : leur état, d'après le corps de la PR ;
- **CI** : `gh pr checks <pr>`. Si des checks sont en cours, propose d'attendre (`gh pr checks <pr> --watch`) ;
- **Points d'attention** : findings mineurs restants, écarts au plan, migrations,
  changements de configuration ;
- **Mergeabilité** : conflits, approbations requises (`gh pr view --json mergeable,reviewDecision`).

Rappelle à l'humain qu'il peut relire la PR sur GitHub (donne le lien) ou tester la branche
en local.

## 2. Décision

Propose : **Merger**, **Demander des modifications**, ou **Mettre en attente**.

- **Merger** : refuse si la CI est en échec, sauf si l'humain confirme explicitement en
  connaissant l'échec. Ensuite :
  1. `gh pr merge <pr> --squash --delete-branch` ;
  2. vérifie que l'issue est fermée (sinon, `gh issue close <n>`) et retire le label
     `status:review` ;
  3. reviens sur la branche principale et mets-la à jour (`git switch <principale> && git pull`).
- **Demander des modifications** : recueille les retours de l'humain, poste-les en
  commentaire de PR (une puce par retour, précise et actionnable), puis passe le label à
  `status:planned`. La suite est `/canal:build <n>`, qui reprendra ces retours.
- **Mettre en attente** : ne change rien.

## Fin

Après un merge, propose la story suivante : la première story `status:ready` sans
dépendance ouverte, ou une story `status:planned`.
