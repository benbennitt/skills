# Skills

Custom agent skills for AI coding assistants. These skills follow the [Agent Skills Standard](https://skills.sh/) and work with Claude Code, OpenClaw, Cursor, Codex, and other compatible tools.

## Install

```bash
npx skills add benbennitt/skills
```

Or install a specific skill:

```bash
npx skills add benbennitt/skills --skill new-project
```

## Skills

| Skill | Description |
|-------|-------------|
| [new-project](./new-project/) | Scaffold a new project with proper CLAUDE.md, skills, MCP config, and docs structure |

## Structure

Each skill is a folder containing a `SKILL.md` with frontmatter and instructions:

```
skill-name/
└── SKILL.md
```

## License

MIT
