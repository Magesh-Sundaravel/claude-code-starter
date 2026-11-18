# `paths:` frontmatter (path-specific rules)

Scope a rule to certain files, so it only enters context when it's relevant.

```markdown
---
paths:
  - "**/*.py"
---

When you read a .py file, start like this 🐍 python rule loaded
```

## No `paths:` vs `paths:`

| | No `paths:` | With `paths:` |
|---|---|---|
| Enters context | At launch, every session | When Claude Reads / Writes / Edits a matching file (or `cat`/`head` on one file) |
| Token cost | Always | Only when relevant |
| Who decides "is this Python?" | Claude, from your plain-English condition | Claude Code, by glob match |
| `/memory` at startup | Listed | Not listed until triggered |

A rule with no `paths:` **is** followed. "When you read a .py file…" written in the text
still works. But the rule costs tokens every session, and you're relying on Claude to remember the condition.

## Glob patterns

Relative to the rule's base directory (project: folder holding `.claude/`; user rules: primary working dir).

| Pattern | Matches |
|---|---|
| `**/*.ts` | `.ts` anywhere |
| `src/**/*` | Everything under `src/` |
| `*.md` | `.md` anywhere |
| `/*.md` | `.md` only at the base dir root |
| `src/components/*.tsx` | `.tsx` directly in that folder, no subfolders |

Multiple patterns are fine, and so is brace expansion (e.g. `src/**/*.{ts,tsx}`).

## Gotchas we hit

- **Only the first `---` block counts as frontmatter.** We ended up with two blocks; the second was just body text.
- **Broken YAML** (missing closing `"` in `"**/*.py`) would break the pattern. Check your quotes.
- Path-scoped rules **don't load** from `--add-dir` directories, or from symlinks to targets outside the project.

## Experiment we ran

1. `python.md` had no `paths:`, so it loaded at launch and was visible in context.
2. Read empty `hello.py` → answer started with 🐍. The trigger is reading the file, not what's in it.
3. Added `paths: ["**/*.py"]` → after `/clear`, `python.md` is not in `/memory` until a `.py` file is read.

## Trade-off: lazy rules and compaction

A path-scoped rule loads into **message history** when Claude reads, writes or edits a matching file. That has two consequences:

| Effect | Detail |
|---|---|
| Not in `/context` Memory files | `/context` lists what loaded at launch there; a lazily loaded rule counts under Messages (observed in the claude-demo test; docs say these files load into message history) |
| Compaction summarizes it away | After `/compact` or auto-compaction the rule text is gone until Claude touches a matching file again, then Claude Code reloads it on demand |

If a rule must stay active without waiting for a matching file, the docs say to drop `paths:` or move it to the project-root CLAUDE.md. The cost: it then loads every request, even in sessions that never touch those files. If it must hold **every time**, use a hook instead.

Same behavior for nested CLAUDE.md files: see `notes/01-memory/nested.md`.

Source: https://code.claude.com/docs/en/context-window (What survives compaction)

## Quiz (answered ✅)

1. Read `hello.py`: does the rule fire? **Yes**, 🐍 appeared.
2. With `paths: ["**/*.py"]`, is `python.md` in context at startup? **No**, it loads only when a `.py` file is read.

Source: https://code.claude.com/docs/en/memory#path-specific-rules
