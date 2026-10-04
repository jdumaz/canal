---
name: plan
description: Fait planifier une story (issue GitHub) par l'agent planner, soumet le plan à validation humaine, puis crée la branche story et y commite le plan validé.
argument-hint: "<numéro d'issue>"
disable-model-invocation: true
---

# /canal:plan — planifier une story

Issue : `$ARGUMENTS`

Le modèle de plan se trouve dans `templates/plan.md`, relatif au dossier de ce skill.

## 1. Préconditions

- `gh issue view <n> --json number,title,body,labels,state` : l'issue est ouverte et porte
  le label `story`. Sinon, arrête-toi et propose `/canal:stories`.
- Les dépendances mentionnées dans l'issue sont fermées. Sinon, préviens l'humain et
  demande s'il veut continuer quand même.
- Lis `AGENTS.md` pour connaître la branche principale.
- Si `docs/plans/<n>-*.md` existe déjà sur la branche de la story, c'est une **replanification** :
  transmets l'ancien plan et la raison (dernier commentaire `status:blocked`, demande de
  l'humain) au planner.

## 2. Planification (autonome)

Lance l'agent `canal:planner` en lui transmettant : le numéro et le contenu de l'issue, le
contenu du modèle de plan, et le contexte de replanification s'il y en a un.

Sans sous-agents disponibles (autre outil que Claude Code), joue toi-même le rôle décrit
dans `../../agents/planner.md`, relatif au dossier de ce skill.

## 3. Écluse : validation du plan

Présente à l'humain une **synthèse** : l'approche en 3 lignes, les étapes (titres), les
fichiers principaux touchés, les risques et les questions ouvertes. Le plan complet n'est
affiché que sur demande. Propose trois choix : **Valider**, **Modifier** (l'humain donne
ses remarques, tu fais réviser le plan puis tu représentes la synthèse), ou **Abandonner**.

S'il reste des questions ouvertes, obtiens les réponses avant validation et intègre-les au
plan.

**Ne passe jamais à l'étape 4 sans validation explicite.**

## 4. Enregistrement

1. Slug : 3 à 5 mots en kebab-case, tirés du titre de l'issue.
2. Pars d'une branche principale à jour (`git fetch`, puis `git switch -c story/<n>-<slug>
   origin/<principale>`), ou réutilise la branche si elle existe déjà. Le working tree doit
   être propre : sinon, arrête-toi et demande quoi faire.
3. Écris `docs/plans/<n>-<slug>.md` avec, dans le frontmatter, `status: validated` et la
   date de validation.
4. Commit : `docs(plan): plan de la story #<n>`.
5. Poste un commentaire sur l'issue avec la synthèse et le chemin du plan.
6. Labels : retire `status:ready` et `status:blocked`, ajoute `status:planned`.

## Fin

Prochaine étape : `/canal:build <n>`.
