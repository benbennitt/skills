---
name: new-project
description: Scaffold a new project with proper CLAUDE.md, skills structure, MCP config, and documentation layout so AI coding agents work effectively from day one. Use when starting any new project, or when retrofitting an existing project with proper agent configuration.
---

# New Project Setup

Scaffold or retrofit a project so AI coding agents (Claude Code, Codex, etc.) can work effectively from the start.

## When to Use This Skill

- Starting a new project from scratch
- Retrofitting an existing project that's missing agent config
- Auditing a project's agent-readiness

## What Gets Created

### 1. CLAUDE.md (project root)

The project briefing that Claude Code reads automatically on every session. Keep it **lean and factual** — no tutorials, no skill-level detail.

**Must include:**
- What the project is (one sentence)
- Tech stack and key dependencies
- Architecture overview (routes, key directories, data model)
- Code conventions (naming, patterns, file organization)
- Pointers to important docs: "Read `docs/X.md` before doing Y"
- Dev server port and how to run
- Build/test commands
- On-completion hook (e.g., `openclaw system event` notification)

**Must NOT include:**
- Framework tutorials (that's what skills are for)
- Long instruction lists that get ignored
- Duplicated info from docs/ files

**Template:**
```markdown
# CLAUDE.md — [Project Name]

## What This Is
[One sentence description]

## Tech Stack
- **Framework:** [e.g., Next.js 16, App Router, React 19, TypeScript strict]
- **Styling:** [e.g., Tailwind CSS v4, shadcn/ui]
- **Database:** [e.g., Supabase PostgreSQL]
- **Testing:** [e.g., Vitest + Testing Library]

## Architecture

### Routes / Entry Points
[List main routes or modules with brief descriptions]

### Key Directories
[Directory tree with what lives where]

### Data Model
[Core entities and relationships, or point to schema file]

## Conventions
- [Code style rules that matter]
- [Component patterns]
- [API patterns]

## Docs
All documentation lives in `docs/`. Read what's relevant before starting work.
- `docs/[IMPORTANT].md` — [when to read it]

## Commands
- `npm run dev` — Dev server on port [PORT]
- `npm run build` — Production build (must pass with zero errors)
- `npm run test:run` — Run tests

## On Completion
[Notification hook, e.g.:]
Run: `openclaw system event --text "Done: [brief summary]" --mode now`
```

### 2. .claude/skills/ (project-level)

Project-specific skills that Claude Code auto-discovers. These contain domain knowledge unique to this project.

```
.claude/
└── skills/
    └── [project-conventions]/
        └── SKILL.md
```

**When to create project-level skills:**
- Project has unique patterns agents keep getting wrong
- Domain-specific knowledge (business logic, API contracts)
- Component patterns that differ from framework defaults

**When to use global skills instead:**
- General framework best practices (Next.js, React, Tailwind)
- Tool usage (shadcn, Drizzle, Prisma)
- These belong in `~/.claude/skills/` via `npx skills add -g`

### 3. .mcp.json

MCP server connections for the project. Claude Code reads this automatically.

```json
{
  "mcpServers": {
    "server-name": {
      "type": "http",
      "url": "https://..."
    }
  }
}
```

### 4. docs/ directory

All project documentation in one place. No docs scattered at the project root.

```
docs/
├── [TOPIC].md          — Active documentation agents should reference
└── archive/            — Historical docs, not relevant to active work
```

**Guideline:** If a doc is important enough that agents should read it before certain tasks, reference it in CLAUDE.md with a clear trigger: "Read `docs/DATABASE-RULES.md` before any schema change."

## Scaffolding Steps

### For a new project:

1. Create the project directory and initialize (git, package.json, etc.)
2. Create `CLAUDE.md` using the template above, filled in with project specifics
3. Create `docs/` directory
4. Create `.mcp.json` if the project uses external services
5. Install relevant global skills: `npx skills add [repo] -g`
6. Create project-level skills in `.claude/skills/` if needed
7. Verify: no broken symlinks, MCP servers reachable

### For an existing project (retrofit):

1. **Audit** what exists: check for CLAUDE.md, .claude/skills/, .mcp.json, scattered docs
2. **Consolidate** docs into `docs/`, move stale docs to `docs/archive/`
3. **Rewrite or create** CLAUDE.md — read the codebase first to understand the architecture
4. **Check skills:** `ls -la .claude/skills/` — fix broken symlinks, install missing skills
5. **Check MCP:** verify `.mcp.json` servers are reachable
6. **Verify:** spawn a test agent and confirm it picks up the right context

## Skill & MCP Health Check

Run these to verify a project is properly configured:

```bash
# Check for broken skill symlinks
find .claude/skills/ -type l ! -exec test -e {} \; -print 2>/dev/null

# Check global skills
find ~/.claude/skills/ -type l ! -exec test -e {} \; -print 2>/dev/null

# List all active skills (project + global)
echo "=== Project ===" && ls .claude/skills/ 2>/dev/null
echo "=== Global ===" && ls ~/.claude/skills/ 2>/dev/null

# Check MCP config exists
cat .mcp.json 2>/dev/null || echo "No .mcp.json found"
```

## Common Mistakes

- **CLAUDE.md too long** — agents skim or ignore walls of text. Keep it under 100 lines.
- **Skills in wrong directory** — `skills/` is for OpenClaw, `.claude/skills/` is for Claude Code. They're different systems.
- **Broken symlinks** — global skills installed via symlink can break if the source is removed. Check periodically.
- **Docs at project root** — creates clutter, agents may not find them. Put everything in `docs/`.
- **Duplicating skill content in CLAUDE.md** — let skills be skills. CLAUDE.md should point to them, not repeat them.
- **No on-completion hook** — without it, orchestrators don't know the agent finished.
