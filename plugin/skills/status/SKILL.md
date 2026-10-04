---
name: status
description: Affiche l'état du workflow canal dans le projet — stories par statut, PR ouvertes, blocages — et la prochaine action recommandée. Lecture seule.
argument-hint: "(aucun argument)"
---

# /canal:status — où en est le flux

Cette commande est en **lecture seule** : tu ne modifies rien.

1. Vérifie que le projet est configuré (bloc `canal:start` dans `AGENTS.md`). Sinon,
   propose `/canal:setup`.
2. Collecte :
   - `gh issue list --label story --state open --json number,title,labels --limit 100` ;
   - `gh pr list --state open --json number,title,headRefName,isDraft,reviewDecision` ;
   - pour chaque story `status:blocked`, le dernier commentaire de l'issue (la raison) ;
   - la branche courante et l'état du working tree.
3. Affiche un tableau compact, groupé dans cet ordre : **blocked**, **review**,
   **building**, **planned**, **ready**. Pour chaque story : le numéro, le titre, la PR
   éventuelle et la commande suivante.
   Signale les incohérences : une story sans label `status:*`, une PR `story/*` dont
   l'issue est fermée, une story `status:building` sans build en cours.
4. Termine par **une** recommandation : la prochaine action la plus utile. L'ordre de
   priorité est : débloquer, puis merger, puis construire, puis planifier.
