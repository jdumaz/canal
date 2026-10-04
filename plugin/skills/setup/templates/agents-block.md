<!-- canal:start -->
## Workflow canal

Ce projet suit le workflow [canal](https://github.com/jdumaz/canal). Règles impératives :

- **Aucun code sans plan validé**, sauf via `/canal:fix` pour une petite correction.
- Une story = une issue GitHub (label `story`) = une branche `story/<n>-<slug>` = une PR.
- Les plans vivent dans `docs/plans/<n>-<slug>.md`, et les specs dans `docs/specs/`.
- Commits en Conventional Commits, avec la référence de l'issue (`feat(scope): ... #<n>`).
- **Aucun merge sans accord explicite de l'humain** (`/canal:ship`).
- Ne jamais supprimer ou affaiblir un test pour le faire passer.

Branche principale : `{{BRANCHE_PRINCIPALE}}`

### Commandes de vérification

Toutes doivent passer avant l'ouverture d'une PR.

| Rôle | Commande |
|---|---|
| Tests | `{{TESTS}}` |
| Lint | `{{LINT}}` |
| Format | `{{FORMAT}}` |
| Types / analyse statique | `{{TYPES}}` |
| Build | `{{BUILD}}` |
<!-- canal:end -->
