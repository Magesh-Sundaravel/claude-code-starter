# Skills (`.claude/skills/`)

Reusable instructions Claude follows when relevant, or when you type `/skill-name`. They use **progressive disclosure**: the body loads only when needed.

## Structure

A skill is a **folder** with a required `SKILL.md` (YAML frontmatter + Markdown body).

```
.claude/skills/deploy/
├── SKILL.md        # required, keep under 500 lines
├── reference.md    # loaded only when SKILL.md points to it
└── scripts/        # executed, not loaded
```

```yaml
---
name: deploy
description: Deployment checklist. Run before deploying.
disable-model-invocation: true
allowed-tools: Bash(git status *)
---
Deploy $ARGUMENTS to production: ...
```

| Field | Purpose |
|---|---|
| `name` | Command name (defaults to folder name) |
| `description` | What it does and when to use it. Put the key use case first |
| `when_to_use` | Extra trigger phrases (shares the description's length cap) |
| `disable-model-invocation` | `true` = only you can invoke it |
| `user-invocable` | `false` = hidden from `/` menu, Claude can still use it |
| `allowed-tools` | Tools usable without asking, only during the turn that invokes it |
| `paths` | Glob patterns, auto-load only when working with matching files |
| `context: fork` | Run in a forked subagent |

Unknown fields are ignored silently. `$ARGUMENTS`, `$0`, `$1` take arguments. `` !`command` `` runs a shell command first and inserts its output.

## Where skills live

| Location | Path | Scope |
|---|---|---|
| Enterprise | Managed settings dir | Whole org |
| Personal | `~/.claude/skills/<name>/SKILL.md` | All your projects |
| Project | `.claude/skills/<name>/SKILL.md` | This repo, commit to share |
| Plugin | `<plugin>/skills/<name>/SKILL.md` | Where plugin is enabled (`/plugin:skill`) |

Name clash: **enterprise > personal > project**. Plugin skills are namespaced, so no clash.

## How skills load (progressive disclosure)

| When | In context |
|---|---|
| Session start | Only **name + description** of each skill |
| Skill invoked | Full `SKILL.md` body, stays across turns |
| Supporting files | Only when `SKILL.md` tells Claude to read them |

- Listing text (`description` + `when_to_use`) is cut at **1,536 characters**.
- The `allowed-tools` grant clears on your next message, even though the instructions remain.

## Who can invoke (and what's in context)

| Frontmatter | You (`/name`) | Claude (auto) | In context at start |
|---|---|---|---|
| default | Yes | Yes | Description in context, body on invoke |
| `disable-model-invocation: true` | Yes | Not on its own | **Nothing**, not even the description |
| `user-invocable: false` | No | Yes | Description in context, body on invoke |

## Skills vs CLAUDE.md

| | CLAUDE.md | Skill |
|---|---|---|
| Loads | Always, every session | On demand |
| Good for | Facts | Procedures, checklists, long reference |
| Cost | Tokens every session | Almost nothing until used |

Make a skill when you keep pasting the same checklist, or a CLAUDE.md section "has grown into a procedure rather than a fact".

## Quiz (answered)

1. A 300-line deploy checklist that runs **only** on `/deploy` → add **`disable-model-invocation: true`**. My answer (name + description saying "don't use when developing") was wrong: a description is only a hint, the frontmatter field is the control.
2. Body in context at session start → **nothing**. ✅ But the description is in context only for default and `user-invocable: false` skills. With `disable-model-invocation: true` **nothing** loads, not even the description.

Source: https://code.claude.com/docs/en/skills
