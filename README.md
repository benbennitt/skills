# Skills

These skills are intended for use with Claude Code, OpenClaw, Cursor, Codex, and other compatible tools. I'm learning, so these skills are highly exploratory and a work-in-progress. 🧪 🤖 ⚒️ 

The structure here is inspired by others like [@shpigford/skills](https://github.com/shpigford/skills), and the skills follow the [agentskills.io/specification](https://agentskills.io/specification). Find more info and better skills at [skills.sh](https://skills.sh).

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
| [self-enhance](./self-enhance/) | Review recent work, identify process gaps, and produce specific file edits to prevent repeated mistakes |

## Structure

Each skill is a folder containing a `SKILL.md` with frontmatter and instructions:

```
skill-name/
└── SKILL.md
```

## License

MIT
