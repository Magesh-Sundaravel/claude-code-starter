# `/memory` command

An in-session menu for your memory files. It shows **where** they can live,
it doesn't print what's in them.

## What it does

| Feature | Detail |
|---|---|
| Lists memory files | User + project scope: `~/.claude/CLAUDE.md`, `CLAUDE.md`, `CLAUDE.local.md`, etc. |
| Missing files | User/project CLAUDE.md entries are listed even if they don't exist yet; picking one **creates** it |
| Open in editor | Pick a file → opens in your editor |
| Auto memory toggle | On/off, saves `autoMemoryEnabled` to **user** settings (`~/.claude/settings.json`) |
| Open auto memory folder | `~/.claude/projects/<project>/memory/` |

## Editor behavior

| Editor type | What happens |
|---|---|
| GUI (VS Code) | Separate window, keep chatting while editing |
| Terminal (Vim, nano) | Takes over the terminal until you quit |

## Toggle scope gotcha

The `/memory` toggle writes to **user** settings, so it affects every project.
To turn auto memory off for one project only, put this in that project's settings:

```json
{ "autoMemoryEnabled": false }
```

## `/memory` vs `/context all`

| Command | Answers |
|---|---|
| `/memory` | Where can memory files live? Open/edit them |
| `/context all` → **Memory files** | Which files actually **loaded** this session |

Nested CLAUDE.md files don't show under **Memory files**, because they load on demand.

## Remember vs CLAUDE.md

- "remember X" → Claude saves it to **auto memory**
- "add X to CLAUDE.md" → goes into **CLAUDE.md**

## Quiz (answered ✅)

1. Pick `CLAUDE.local.md` in `/memory`: what opens? **VS Code, separate window, keep chatting.**
2. Auto memory on or off? **On**: default, no `autoMemoryEnabled` override anywhere, and `/context` shows an AutoMem `MEMORY.md` loaded.

Source: https://code.claude.com/docs/en/memory
