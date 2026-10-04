---
name: build
description: Construit une story planifiée en totale autonomie — boucle implementer ⇄ reviewer (3 tours au maximum) jusqu'à zéro finding bloquant, puis push et ouverture de la PR. Reprend aussi les retours humains d'une PR existante.
argument-hint: "<numéro d'issue>"
disable-model-invocation: true
---

# /canal:build — construire une story

Issue : `$ARGUMENTS`

Cette étape est **autonome** : tu ne poses aucune question à l'humain, sauf si une
précondition échoue. Tu t'arrêtes et tu rends la main dans les cas prévus ci-dessous.

## 1. Préconditions

- L'issue est ouverte et porte le label `story`.
- La branche `story/<n>-*` existe et contient `docs/plans/<n>-*.md` avec `status: validated`.
  Sinon, arrête-toi : « Pas de plan validé, lance `/canal:plan <n>` ».
- Bascule sur cette branche. Le working tree doit être propre. Synchronise-la avec la
  branche principale (`git fetch` puis `git rebase origin/<principale>` si elle n'a jamais
  été poussée, `git merge` sinon). En cas de conflit, arrête-toi et rends la main.
- **Mode reprise** : si une PR est déjà ouverte pour cette branche
  (`gh pr list --head <branche>`), récupère les retours humains postés depuis le dernier
  push (`gh pr view <pr> --comments`, et les commentaires de revue avec
  `gh api repos/{owner}/{repo}/pulls/<pr>/comments`). Ils deviennent les findings du
  premier tour.

Labels : retire `status:planned`, `status:review` et `status:blocked`, puis ajoute
`status:building`.

## 2. Boucle (3 tours au maximum)

Sans sous-agents disponibles, joue toi-même chaque rôle à partir de `../../agents/`
(relatif au dossier de ce skill), en repartant d'un regard neuf pour la revue.

**Tour 1 — implémentation** (ou correction des retours humains en mode reprise)
Lance l'agent `canal:implementer` avec : le numéro de l'issue, le chemin du plan, et les
findings à traiter s'il y en a.
- `STATUT: PLAN_INVALIDE` → va à l'étape 4 (blocage) avec l'explication.
- `STATUT: ECHEC` → passe quand même à la revue : elle qualifiera le problème.

**Revue**
Lance un **nouvel** agent `canal:reviewer` (jamais le même contexte que l'implémentation)
avec : le numéro de l'issue, le chemin du plan et la branche principale (diff
`origin/<principale>...HEAD`).
- `VERDICT: APPROUVE` → sors de la boucle.
- `VERDICT: CHANGEMENTS_REQUIS` → s'il reste des tours, lance un nouvel implementer avec
  **uniquement** les findings BLOQUANT et IMPORTANT, puis refais une revue.

Après 3 tours sans approbation, va à l'étape 4.

## 3. Livraison

1. `git push -u origin <branche>`.
2. S'il n'y a pas encore de PR, crée-la avec `gh pr create --base <principale>` :
   - Titre : le titre de l'issue, au format Conventional Commits (`feat: …`, `fix: …`).
   - Corps : `Closes #<n>`, le résumé des changements, le lien vers le plan, les critères
     d'acceptation cochés, le résultat des vérifications, les findings MINEURS restants et
     les écarts au plan.
   S'il y a déjà une PR (mode reprise), poste un commentaire qui résume les corrections, en
   regard de chaque retour.
3. Labels : retire `status:building` et ajoute `status:review`.
4. Rapporte à l'humain : le lien de la PR, le nombre de tours, les vérifications, et les
   points à regarder en priorité. Prochaine étape : `/canal:ship <n>`.

## 4. Blocage

Les commits déjà faits restent sur la branche locale. Ne pousse pas.
Poste sur l'issue un commentaire qui explique la raison (plan invalide, non-convergence,
conflit), les findings restants et ce qui est attendu de l'humain. Labels : retire
`status:building` et ajoute `status:blocked`. Rapporte la même chose à l'humain, avec la
suite conseillée : `/canal:plan <n>` pour replanifier, ou une correction manuelle puis
`/canal:build <n>`.
