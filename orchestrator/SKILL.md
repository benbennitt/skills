---
name: orchestrator
description: Patterns for AI orchestrator agents that spawn and manage Claude Code coding agents. Covers correct invocation, pre-flight checks, progress monitoring, output review, and task lifecycle. Use when delegating implementation work to Claude Code.
---

# Orchestrator Skill

How to effectively spawn, manage, and review Claude Code coding agents from an orchestrator (CORA, IRIS, or similar).

## Core Concept

Orchestrators plan work and review output. Coding agents do implementation. The only bridge between them is:
1. The **prompt** you write
2. The project's **CLAUDE.md** (auto-read by Claude Code)
3. The project's **`.claude/skills/`** (auto-discovered)
4. The project's **`.mcp.json`** (auto-loaded)

Your skills, memory, and context do NOT transfer to the coding agent.

## Spawning a Coding Agent

### Correct Invocation

Always use stdin pipe + `-p` (print mode) + `--allowedTools`:

```bash
cat <<'PROMPT' | claude -p --allowedTools "Edit,Write,Read,MultiEdit,Bash(npm run build),Bash(npm run test:run)"
[your task prompt here]

When completely finished, run: openclaw system event --text "Done: [summary]" --mode now
PROMPT
```

### Never Do This

```bash
# WRONG: Interactive mode, blocks on approvals
claude "do the thing"

# WRONG: Positional prompt + --allowedTools = error
claude --allowedTools "Edit,Write" "do the thing"

# WRONG: Blanket permissions
claude --dangerously-skip-permissions "do the thing"
```

### Standard allowedTools

For most project work:
```
Edit,Write,Read,MultiEdit,Bash(npm run build),Bash(npm run test:run)
```

Extend per task:
- `Bash(cat *)` — reading files
- `Bash(ls *)`, `Bash(find *)` — discovery
- `Bash(grep *)` — searching
- `Bash(git *)` — git operations
- `Bash(rm *)` — file deletion (use sparingly)

## Pre-Flight Checklist

Before spawning every agent:

1. **Verify project config exists** — `CLAUDE.md`, `.claude/skills/`, `.mcp.json` are present and current
2. **Check actual state** — read the DB schema, filesystem, or API to understand current state. Never assume.
3. **Backup if touching DB** — run the project's backup script before schema changes
4. **Scope the allowedTools** — only grant what the task needs

## Writing Good Prompts

### Do
- Be specific about what files to create/modify
- Reference actual file paths, function names, component names
- Include verification steps ("Run npm run build and ensure zero errors")
- End with the completion notification command
- Mention constraints ("Do NOT touch app/api/skills/health/")

### Don't
- Write vague instructions ("make it better")
- Assume the agent knows your project conventions (that's what CLAUDE.md is for)
- Include orchestration context the agent doesn't need
- Forget the build verification step

### Prompt Structure

```
Task: [one-line summary]

[Context — what exists now, what needs to change]

## Changes needed:

### 1. [File or area]
[Specific instructions]

### 2. [File or area]
[Specific instructions]

## Verify
Run npm run build and ensure zero errors.

## Do NOT touch:
- [files/areas to protect]

When completely finished, run: openclaw system event --text "Done: [summary]" --mode now
```

## Monitoring Progress

- `-p` mode buffers all output until completion — no streaming logs
- Track progress via `git status` or `git diff --stat` in the project directory
- The `openclaw system event` at the end triggers immediate notification
- If no file changes after 5+ minutes, something is wrong — check the process

## Reviewing Output

After the agent completes:

1. **Check build** — `npm run build` should pass clean
2. **Review the diff** — `git diff --stat` for scope, `git diff` for content
3. **Verify the task was fully completed** — not just partially done
4. **Test if possible** — run the dev server, check the UI, run tests
5. **Use the code-review skill** for thorough review on important changes

## Task Lifecycle

```
ready → doing (pick up, update status)
  → spawn agent with good prompt
  → monitor progress
  → review output
  → fix issues (either directly or re-spawn)
doing → review (move when verified, not before)
```

- Move to Review only when work is verified
- Never ask "want to review?" — just move it
- If the agent's output needs fixes, fix them before moving to Review
- Ben moves tasks from Review to Done

## Common Failure Modes

| Symptom | Cause | Fix |
|---------|-------|-----|
| No file changes after 5min | Interactive mode / permission block | Kill and re-spawn with `-p` + `--allowedTools` |
| Agent creates wrong files | Vague prompt, missing context | Rewrite prompt with specific file paths |
| Build fails after agent | Agent didn't run build verification | Add explicit build step to prompt |
| Agent ignores project patterns | CLAUDE.md is missing or thin | Improve CLAUDE.md before re-spawning |
| MCP connection errors | .mcp.json misconfigured | Verify MCP health before spawning |
