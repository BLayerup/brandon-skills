# brandon-skills

Personal Claude / Cowork plugin marketplace.

## Marketplace

- **brandon-skills** (this repo) — marketplace container.

## Plugins

Each plugin lives in its own subdirectory. The marketplace.json `source.path` field points to the plugin root.

| Plugin | Subdir | Description |
|---|---|---|
| `cowork-prefs` | [`cowork-prefs/`](cowork-prefs/) | Brandon's personal Claude/Cowork preferences (writing, clarification, project handling). |
| `layerup-video-sort` | [`layerup-video-sort/`](layerup-video-sort/) | Sort and organize unedited Layer Up phone videos from `~/Downloads` into Drafts/ topic folders, with per-folder CapCut AI Clipper prompts. |

## Skills

### Inside `cowork-prefs`
- **conversational-questioning** — caps `AskUserQuestion` calls at 1 question per round by default (2 when tightly coupled, 3 max, never 4).

### Inside `layerup-video-sort`
- **video-sort** — full pipeline for deduping phone clips, generating contact sheets for AI topic identification, sorting into `Drafts/<YYYY-MM-DD>_<topic>/` folders, and writing per-folder CapCut prompts with Layer Up brand defaults.

## Install

Two steps from inside Claude Code or Cowork:

```
/plugin marketplace add BLayerup/brandon-skills
/plugin install cowork-prefs@brandon-skills
/plugin install layerup-video-sort@brandon-skills
```

Run all three commands to enable both plugins.

## Repo layout

```
brandon-skills/
├── .claude-plugin/
│   └── marketplace.json          # lists all plugins, each with source.path
├── cowork-prefs/                  # plugin root
│   ├── .claude-plugin/plugin.json
│   └── skills/
│       └── conversational-questioning/SKILL.md
├── layerup-video-sort/            # plugin root
│   ├── .claude-plugin/plugin.json
│   └── skills/
│       └── video-sort/SKILL.md
└── README.md
```

## Add a new skill

1. Pick the plugin subdir it belongs in (or create a new plugin subdir if it's a new domain).
2. Add a folder under that plugin's `skills/`.
3. Add a `SKILL.md` with YAML frontmatter (`name`, `description`) and the skill body.
4. Bump the `version` field in that plugin's `.claude-plugin/plugin.json`.
5. Commit and push. Reinstall the plugin to pick up changes.

## Add a new plugin

1. Create a new top-level subdir, e.g. `<plugin-name>/`.
2. Add `<plugin-name>/.claude-plugin/plugin.json` with `name`, `version`, `description`, `author`, `license`, `keywords`.
3. Add `<plugin-name>/skills/<skill-name>/SKILL.md` for each skill.
4. Add an entry to `.claude-plugin/marketplace.json` under `plugins`, with `source.path: "<plugin-name>"`.
5. Update this README.
6. Commit, push, run `/plugin marketplace update brandon-skills` then `/plugin install <plugin-name>@brandon-skills`.

## License

MIT.
