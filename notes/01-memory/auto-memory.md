# Auto memory

Notes **Claude writes itself** (your corrections, preferences, project quirks).
CLAUDE.md is the opposite: **you** write it.

## CLAUDE.md vs auto memory

| | CLAUDE.md | Auto memory |
|---|---|---|
| Who writes it | You | Claude |
| Contains | Instructions and rules | Learnings and patterns |
| Loaded | Every session | Every session (`MEMORY.md`: first 200 lines or 25KB) |

Rule of thumb: rules you decide on go in CLAUDE.md. If Claude saved something
you want as a rule, move it to CLAUDE.md and delete the memory (we did this with
`write-notes-per-topic.md`).

## Folder layout

```
~/.claude/projects/<project>/memory/
├── MEMORY.md     # index, one line per memory, loaded every session
└── <topic>.md    # one memory each, read only when relevant
```

Each memory file has frontmatter (`name`, `description`, `type`), a `modified`
timestamp Claude Code adds automatically, and **Why:** / **How to apply:** lines.

## Which folder? (`<project>`)

| Where you start `claude` | Memory folder based on |
|---|---|
| Inside a git repo (any subfolder, any worktree) | The **repo**, so one shared folder |
| Outside git | The **folder you started in**, so separate folders |

## On / off

| How | Scope |
|---|---|
| `/memory` toggle | Writes `autoMemoryEnabled` to **user** settings, so all projects |
| `"autoMemoryEnabled": false` in project settings | That project only |

Default is **on**. `autoMemoryDirectory` in settings moves the folder.

## Experiment we ran

1. Deleted the duplicate `write-notes-per-topic.md` and its line in `MEMORY.md`
2. New session → `/context` → the **AutoMem** row was gone (empty `MEMORY.md`, nothing to load)

## Quiz (answered ✅)

1. AutoMem in `/context` after emptying `MEMORY.md`? **No.**
2. `claude` in a repo subfolder: same or new memory folder? **Same** (first answered "new" ❌, it's derived from the git repo).
3. Not a git repo, `~/scratch/a/` vs `~/scratch/b/`? **Separate**, the start folder is used.

Source: https://code.claude.com/docs/en/memory
