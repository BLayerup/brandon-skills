# brandon-skills

Personal Claude / Cowork skills.

## Skills included

- **conversational-questioning** — caps `AskUserQuestion` calls at 1 question per round by default (2 when tightly coupled, 3 max, never 4). Designed to make clarification feel like a conversation rather than a survey.

## Install

From inside Cowork or Claude Code:

```
/plugin install <github-user>/<repo-name>
```

Replace `<github-user>/<repo-name>` with the path to wherever this repo is hosted.

## Add a new skill

1. Create a new folder under `skills/`.
2. Add a `SKILL.md` file with YAML frontmatter (`name`, `description`) and the skill body.
3. Bump the `version` field in `.claude-plugin/plugin.json`.
4. Commit and push. Reinstall the plugin to pick up changes.

## License

MIT.
