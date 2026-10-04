---
name: setup
description: Installe ou met à jour le workflow canal dans le projet courant — détecte la stack et les commandes de vérification, écrit le bloc canal d'AGENTS.md, crée les labels GitHub et les dossiers docs/. Idempotent.
argument-hint: "(aucun argument)"
disable-model-invocation: true
---

# /canal:setup — configurer le projet

Tu configures le projet courant pour le workflow canal. Cette commande est **assistée** :
tu détectes, tu proposes, l'humain confirme. Tu peux la relancer sans risque : elle met à
jour ce qui existe sans rien dupliquer.

Le modèle du bloc AGENTS.md se trouve dans `templates/agents-block.md`, relatif au dossier
de ce skill.

## 1. Prérequis (bloquants)

Vérifie, et arrête-toi avec une explication claire si l'un d'eux échoue :
- le dossier est un dépôt git (`git rev-parse --show-toplevel`) ;
- `gh` est installé et authentifié (`gh auth status`) ;
- le dépôt a un remote GitHub (`gh repo view --json nameWithOwner,defaultBranchRef`).
  Note la branche principale.

## 2. Détection de la stack

Inspecte les fichiers présents (`composer.json`, `artisan`, `package.json` et ses scripts,
`pyproject.toml`, `go.mod`, `Cargo.toml`, `Makefile`, `phpunit.xml`, `phpstan.neon*`,
`pint.json`, `eslint.config.*`, `tsconfig.json`, `vite.config.*`, la CI dans
`.github/workflows/`…). La CI est la meilleure source : reprends les commandes qu'elle lance.

Déduis une commande pour chaque rôle, ou « — » si le rôle n'existe pas :
**Tests**, **Lint**, **Format** (en mode vérification, sans écrire), **Types / analyse
statique**, **Build**.

Exemple pour Laravel + Inertia + Vue : `php artisan test` (ou `vendor/bin/pest`),
`vendor/bin/pint --test`, `vendor/bin/phpstan analyse` si présent, `npm run lint`,
`npx vue-tsc --noEmit` si TypeScript, `npm run build`.

Ne propose que des commandes dont tu as vérifié l'existence (script npm présent, binaire
dans `vendor/bin`, etc.).

## 3. Confirmation

Présente à l'humain, de façon compacte : la stack détectée, la branche principale et le
tableau des commandes. Demande-lui de valider ou de corriger. N'écris rien avant sa
réponse.

## 4. Écriture

1. **AGENTS.md**
   - S'il n'existe pas : crée-le avec un titre `# <nom du projet>`, puis le bloc rempli, puis
     une section `## Conventions du projet` contenant quelques puces déduites du code
     (architecture, nommage, emplacement des tests) et marquées « à compléter ».
   - S'il existe : remplace uniquement ce qui se trouve entre `<!-- canal:start -->` et
     `<!-- canal:end -->`, ou ajoute le bloc à la fin s'il est absent. Ne touche à rien
     d'autre.
2. **CLAUDE.md** : s'il n'existe pas, crée-le avec la seule ligne `@AGENTS.md`. S'il existe
   sans importer AGENTS.md, propose d'ajouter `@AGENTS.md` en première ligne et attends
   l'accord.
3. **Dossiers** : `docs/specs/` et `docs/plans/`, avec un `.gitkeep` s'ils sont vides.
4. **Labels GitHub** (idempotent, avec `gh label create <nom> --force --color <c> --description <d>`) :
   | Label | Couleur | Description |
   |---|---|---|
   | `story` | `1D76DB` | Story du workflow canal |
   | `status:ready` | `C2E0C6` | Story prête à planifier |
   | `status:planned` | `0E8A16` | Plan validé, prête à construire |
   | `status:building` | `FBCA04` | Build en cours |
   | `status:review` | `5319E7` | PR ouverte, en attente de revue humaine |
   | `status:blocked` | `B60205` | Intervention humaine requise |
5. **`.claude/settings.json`** : fusionne (sans écraser les autres clés) :
   ```json
   {
     "extraKnownMarketplaces": {
       "canal": { "source": { "source": "github", "repo": "jdumaz/canal" } }
     },
     "enabledPlugins": { "canal@canal": true }
   }
   ```

## 5. Fin

Affiche ce qui a été créé ou modifié. Propose ensuite de committer ces fichiers sur une
branche `chore/canal-setup` et d'ouvrir une PR, mais ne le fais qu'avec l'accord de l'humain.
Termine par la prochaine étape : `/canal:spec <sujet>`, ou `/canal:stories "<besoin>"` pour
une petite feature.
