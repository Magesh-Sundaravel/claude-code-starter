# `@path` imports

A CLAUDE.md can pull in another file with `@path/to/file`. The imported file
is expanded and loaded into context **alongside** the CLAUDE.md that imports it,
as if its text were written right there.

Example in this repo (`CLAUDE.md`):

```
Style guide: @docs/style.md
```

`docs/style.md` says "Always end answers with 🚀", so every answer ends with 🚀.

## Rules

| Rule | Detail |
|---|---|
| Relative paths | Resolve from **the file containing the import**, not the working directory |
| Absolute / home paths | Allowed, e.g. `@~/.claude/my-prefs.md` |
| Nesting | Imported files can import others, max **4 hops** |
| Escape | Backticks or fenced code blocks are skipped: `` `@README` `` stays plain text |
| Spaces | Backslash each space: `@Design\ Docs/x.md`. Quoted paths are not imported |
| External imports | Path outside the project → one-time **approval dialog** (project files only). Decline = stays disabled |
| User-scope files | Imports in `~/.claude/CLAUDE.md` / `~/.claude/rules/` load without the dialog |

## `@path` vs plain mention

| Written in CLAUDE.md | When read | Guaranteed? |
|---|---|---|
| `@docs/style.md` | At session start | ✅ Always in context |
| `` `@docs/style.md` `` or a plain path | Only if Claude opens it with a tool | ❌ Just text |

Being able to *read* a file with a tool is not the same as it being *loaded as
instructions*.

## When is it loaded?

Once, at **session start** (with its parent CLAUDE.md). Editing the imported
file mid-session does not change the running session; restart to pick it up.
Different from nested CLAUDE.md, which lazy-loads (see `nested.md`).

## Experiment we ran

1. Wrapped the import in backticks → `` `@docs/style.md` ``
2. Restarted → Claude Code reported `docs/style.md` no longer loaded, no 🚀
3. Removed backticks, restarted → 🚀 back

## Quiz (answered ✅)

1. Backticks around `@docs/style.md`: is 🚀 still there? **No**, it's plain text, not imported.
2. Edit `docs/style.md` to 🎯 mid-session, no restart: which emoji? **🚀**, the file was loaded at start.

Source: https://code.claude.com/docs/en/memory
