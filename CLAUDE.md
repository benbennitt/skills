# CLAUDE.md — Skills Repo

## What This Is
A collection of custom agent skills for AI coding assistants, following the [agentskills.io/specification](https://agentskills.io/specification). Public repo at github.com/benbennitt/skills.

## Structure
Each skill is a directory with a `SKILL.md` file:
```
skill-name/
└── SKILL.md    — Frontmatter (name, description) + full skill instructions
```

## Conventions
- Skill names are kebab-case
- SKILL.md frontmatter must include `name` and `description`
- Description should be one sentence explaining when to use the skill
- Instructions should be specific and actionable — no vague advice
- Skills produce actions (file edits, commands), not reports
- Update README.md table when adding/removing skills

## Install
```bash
npx skills add benbennitt/skills --skill <name>
```

## Spec
See `SPEC.md` for the skill design philosophy.
