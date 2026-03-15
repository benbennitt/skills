---
name: commit
description: Write clean, consistent git commit messages with sentence case summaries and agent attribution. Use when committing code changes.
---

# Commit Skill

Write clean, consistent git commit messages that are easy to scan and understand.

## Commit Message Format

```
Subject line in sentence case

Optional body that explains why the change was made, wrapped at 72
characters per line. The body should focus on *why* not *what* (the diff
shows what changed).

Author: Agent via <model>
```

## Subject Line Rules

- **Sentence case**: Capitalize only the first word (not a title)
- **Imperative mood**: Start with a verb like Add, Fix, Refactor, Update, Remove
- **Concise**: ~50-72 chars max — summarize the *change*, not the file
- **No period**: Subject lines don't end with punctuation
- **No prefixes**: No `feat:`, `fix:`, or conventional commits notation

### Good Examples

```
Add comprehensive domain search with extensive TLD support
Fix up misc page titles
Refine how invalid cells are styled
Fix up SSR and hide controls on Sudoki game over
Remove deprecated API endpoints
Update user authentication flow
```

### Bad Examples

```
feat: Add new feature          ❌ No conventional commits prefix
Fixed Bug                      ❌ Not imperative mood, capitalized wrong
update code                    ❌ Not capitalized, too vague
Add new feature.               ❌ No trailing period
Updated: user-profile.tsx      ❌ File name as subject
FIX AUTHENTICATION             ❌ No all caps
```

## When to Use a Body

Include a body when:

- Multi-file changes need context
- The *why* isn't obvious from the diff
- There are breaking changes or behavioral shifts
- You need to reference issues, links, or related work

## Anti-Patterns

- ❌ Vague messages: "Update code", "Fix bug", "Changes"
- ❌ File lists as the subject
- ❌ Conventional commits prefixes (`feat:`, `fix:`, `chore:`)
- ❌ ALL CAPS or Title Case Everywhere
- ❌ Trailing periods on subject line
- ❌ Subject longer than 72 characters

## Git Commands

```bash
# Simple commit
git add -A
git commit -m "Add user profile page" -m "Author: Agent via Opus 4.6"

# Commit with body
git add -A
git commit -m "Fix authentication flow" \
  -m "Previous implementation didn't handle expired tokens correctly. Now we refresh tokens before making API calls if they're within 5 minutes of expiry." \
  -m "Author: Agent via Opus 4.6"
```

## Attribution

Always include your model in the last line of the commit description:

```
Author: Agent via Opus 4.6
Author: Agent via Sonnet 4.5
Author: Agent via DeepSeek R1
```

This helps track which models produced which work and improves accountability.
