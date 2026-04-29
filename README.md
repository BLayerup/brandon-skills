# brandon-skills

Personal Claude / Cowork plugin marketplace.

## Marketplace

- **brandon-skills** (this repo) — marketplace container.

## Plugins

- **cowork-prefs** — Brandon's personal Claude/Cowork preferences.

## Skills inside cowork-prefs

- **conversational-questioning** — caps `AskUserQuestion` calls at 1 question per round by default (2 when tightly coupled, 3 max, never 4). Designed to make clarification feel like a conversation rather than a survey.

## Install

Two steps from inside Claude Code or Cowork:

```
/plugin marketplace add <github-user>/<repo-name>
/plugin install cowork-prefs@brandon-skills
```

Replace `<github-user>/<repo-name>` with the GitHub path to this repo (for example, `BLayerup/brandon-skills`).

## Add a new skill

1. Create a new folder under `skills/`.
2. Add a `SKILL.md` file with YAML frontmatter (`name`, `description`) and the skill body.
3. Bump the `version` field in `.claude-plugin/plugin.json`.
4. Commit and push. Reinstall the plugin to pick up changes.

## License

MIT.
