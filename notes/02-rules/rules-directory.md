# `.claude/rules/*.md`

Split project instructions into **one file per topic** instead of one big CLAUDE.md.

## Layout

```
your-project/
├── .claude/
│   ├── CLAUDE.md
│   └── rules/
│       ├── code-style.md
│       ├── testing.md
│       └── frontend/        # subfolders OK (discovered recursively)
│           └── react.md
```

## Key facts

| Fact | Detail |
|---|---|
| Discovery | All `.md` files, **recursively** (subfolders included) |
| When it loads | At launch, **if no `paths:` frontmatter** |
| Priority | Same as `.claude/CLAUDE.md` |
| Naming | One topic per file, descriptive name (`testing.md`, `api-design.md`) |
| Skipped when | `project` excluded from `--setting-sources` |

## Frontmatter

A settings block at the **very top** of a markdown file, between two `---` lines (YAML).

```markdown
---
paths:
  - "**/*.py"
---
- Use type hints everywhere
```

| Part | What | Read by |
|---|---|---|
| Between `---` | Metadata (when/where the file applies) | Claude Code (the tool) |
| Below | Instructions | Claude (the model) |

- Only matters in files Claude Code reads (rules, later skills/agents).
  In `README.md` or `notes/*.md` it's **ignored**.
- Rule with `paths:` loads **when Claude touches a matching file**
  (Read / Write / Edit, or `cat`/`head` on one file), not at launch.

## Rules vs skills

| | Rules | Skills |
|---|---|---|
| In context | Always (or when matching file opened) | Only when invoked or relevant |
| Use for | Things that should always apply | Task-specific instructions |

## Experiment we ran

1. Created `.claude/rules/greeting.md`: *When I say "ping", say "Pong from rules"*
2. `/restart` → `/memory` showed `greeting.md` ✅
3. Typed `ping` → got **Pong from rules** ✅

## Quiz (answered ✅)

1. `greeting.md` in `/memory`? **Yes.**
2. Moved to `.claude/rules/misc/greeting.md`, still loads? **Yes**, discovery is
   **recursive** (first reason given was "all rules load at launch", only true without `paths:`).
3. `paths:` frontmatter in `notes/01-memory/scopes.md` does anything? **No**, ignored,
   `notes/` isn't a folder Claude Code reads instructions from.

Source: https://code.claude.com/docs/en/memory#organize-rules-with-claude/rules/
