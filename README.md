# claude-code-starter

A hands-on learning sandbox for understanding **how Claude Code works inside**:
how it loads memory (`CLAUDE.md`), rules, settings and permissions, skills,
subagents, hooks, MCP, and plugins. You learn when each one loads, which one
wins when they conflict, and what belongs where.

This is not a template to copy. The goal is to learn the principles well enough
to design the right Claude Code setup for any project yourself.

Each feature is learned one step at a time, tested with a small experiment in
this repo, and written up in `notes/` before moving on.

## What's inside

```
.
├── CLAUDE.md          # Project memory: rules, roadmap, current step (committed)
├── CLAUDE.local.md    # Personal project memory (gitignored)
├── .mcp.json          # Project-scoped MCP server (demo, fake URL)
├── .claude/
│   ├── settings.json  # Shared settings: permissions, hooks
│   ├── rules/         # Topic rules (greeting.md, python.md with paths: frontmatter)
│   └── hooks/
│       └── block-rm-rf.sh   # PreToolUse hook script
├── docs/
│   └── style.md       # Imported into CLAUDE.md via @docs/style.md
├── backend/
│   ├── CLAUDE.md      # Nested memory, lazy-loaded when backend/ files are touched
│   └── app.py
└── notes/             # One note per topic, grouped by roadmap section
    ├── 01-memory/     # scopes, nested, imports, /memory, auto memory
    ├── 02-rules/      # rules directory, paths, user rules, symlinks
    ├── 03-settings/   # settings scopes, permissions
    ├── 04-plans/      # plan mode, plansDirectory
    └── 05-extending/  # skills, subagents, hooks, mcp, plugins
```

## Roadmap

| # | Topic | Covers |
|---|-------|--------|
| 1 | Memory (`CLAUDE.md`) | Scopes and load order, nested files, `@path` imports, `/memory`, auto memory |
| 2 | Rules | `.claude/rules/*.md`, `paths:` frontmatter, user rules, sharing via symlinks |
| 3 | Settings & permissions | User / project / local `settings.json`, allow / ask / deny |
| 4 | Plans | Plan mode, `plansDirectory` |
| 5 | Extending | Skills, subagents, hooks, MCP (`.mcp.json`), plugins |
| 6 | Best practices | What goes where, keeping `CLAUDE.md` lean, what to commit, a real FastAPI + React setup |

Progress is tracked with checkboxes in [`CLAUDE.md`](CLAUDE.md). Sections 1 to 5 are done; section 6 is next.

## How to use it

1. Install Claude Code: see the [official docs](https://code.claude.com/docs).
2. Clone this repo and run `claude` from the repo root.
3. Follow the roadmap in `CLAUDE.md`. Claude acts as a tutor: one short step,
   a quick test, then wait.
4. Read `notes/<section>/<topic>.md` for the write-up of each finished topic.
5. Use what you learned to build your own project's setup from scratch.
   Section 6 practices this on a FastAPI + React app.

## What to commit vs ignore

| Commit | Gitignore |
|--------|-----------|
| `CLAUDE.md`, `.claude/CLAUDE.md`, `.claude/rules/`, `.claude/settings.json`, `.claude/skills/`, `.claude/agents/`, `.mcp.json` | `CLAUDE.local.md`, `.claude/settings.local.json` |

## Reference

- Claude Code docs: https://code.claude.com/docs
