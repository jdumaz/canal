# canal

Un workflow d'agentic engineering pour Claude Code, qui guide un flux fluide vers un résultat
structuré : **spec → stories → plan → build → ship**.

Trois agents (planner, implementer, reviewer) avancent seuls là où c'est sûr. L'humain
décide aux écluses : la spec, le découpage, le plan et le merge. Les stories vivent dans
les issues GitHub.

Le détail de la méthode est dans [WORKFLOW.md](WORKFLOW.md).

## Prérequis

- [Claude Code](https://claude.com/claude-code)
- [`gh`](https://cli.github.com/), authentifié (`gh auth login`)

## Installation

Une seule fois par machine, dans Claude Code :

```
/plugin marketplace add jdumaz/canal
/plugin install canal@canal
```

Puis, dans chaque projet :

```
/canal:setup
```

`setup` détecte la stack et les commandes de vérification, écrit le bloc canal d'`AGENTS.md`,
crée les labels GitHub et les dossiers `docs/specs` et `docs/plans`, et déclare le plugin
dans `.claude/settings.json`.

## Mise à jour

```
/plugin marketplace update canal
```

Vous pouvez aussi activer la mise à jour automatique de la marketplace depuis `/plugin`.

## Commandes

| Commande | Mode | Rôle |
|---|---|---|
| `/canal:setup` | assisté | Configurer le projet |
| `/canal:spec <sujet>` | assisté | Rédiger une spec par interview |
| `/canal:stories <spec \| "besoin">` | auto + validation | Découper en issues GitHub |
| `/canal:plan <n>` | auto + validation | Planifier une story |
| `/canal:build <n>` | **autonome** | Implementer ⇄ reviewer, puis PR |
| `/canal:ship <n>` | assisté | Décider du merge |
| `/canal:fix "<desc>"` | assisté | Petite correction sans plan |
| `/canal:status` | auto | État du flux et prochaine action |

## Développer canal

Pour tester une modification locale sans passer par la marketplace :

```
claude --plugin-dir ./plugin
```

Pour valider la structure : `claude plugin validate .`

### Structure

```
.claude-plugin/marketplace.json   marketplace (pointe vers ./plugin)
plugin/
  .claude-plugin/plugin.json      manifeste du plugin (son nom = préfixe des commandes)
  agents/                         planner, implementer, reviewer
  skills/<commande>/SKILL.md      une commande = un skill (format ouvert, réutilisable par Codex)
  skills/<commande>/templates/    modèles utilisés par la commande
WORKFLOW.md                       la méthode
```

### Renommer le projet

Le nom n'apparaît qu'à quelques endroits :

1. `name` dans `.claude-plugin/marketplace.json` (marketplace et plugin) et dans
   `plugin/.claude-plugin/plugin.json` ;
2. les mentions `/canal:` et `canal:` dans les fichiers markdown (un `sed` suffit) ;
3. le dépôt GitHub (GitHub redirige l'ancienne URL).

Dans chaque projet déjà configuré, relancez ensuite `/<nouveau>:setup`. Il réécrit le bloc
d'`AGENTS.md` et `.claude/settings.json`. Les labels, les branches et les dossiers `docs/`
ne contiennent pas le nom et restent inchangés.

## Licence

[MIT](LICENSE)
