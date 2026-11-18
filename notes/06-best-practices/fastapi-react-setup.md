# Real setup: FastAPI + React

Practice project built at `~/Magesh/AI/Agentic_AI/claude-demo/` (outside this repo, its own `git init`, not committed). Start Claude from its root.

## Layout

```
claude-demo/
├── CLAUDE.md                  # repo-wide rules, 13 lines
├── CLAUDE.local.md            # personal, gitignored (not created)
├── .gitignore
├── .claude/
│   ├── settings.json          # permissions.deny + PostToolUse hook
│   ├── hooks/format-python.sh # ruff after edits
│   ├── rules/python.md        # paths: backend/**/*.py
│   ├── rules/react.md         # paths: frontend/src/**/*.js, *.jsx
│   └── skills/add-endpoint/SKILL.md
├── backend/CLAUDE.md          # uv commands, layout, config
└── frontend/CLAUDE.md         # npm commands, layout, config
```

## What goes where

| Need | Feature | Why |
|---|---|---|
| Repo-wide rules (start from root, no secrets, commit prefix) | Root `CLAUDE.md` | Every session needs it |
| Run/test commands and folder layout per area | Nested `backend/` and `frontend/` CLAUDE.md | Load only when working in that area |
| async, type hints, docstrings, Pydantic | `rules/python.md` with `paths:` | Guidance for `.py` files only |
| Function components, one per file, API via `src/api/` | `rules/react.md` with `paths:` | Guidance for `.js/.jsx` only |
| ruff after every Python edit | `PostToolUse` hook, matcher `Edit\|Write` | Must happen every time |
| Never read `.env` | `permissions.deny` | Enforced, not a request |
| "Add an endpoint" procedure | Skill | Needed sometimes; description-only cost until used |

Conventions live in rules; commands and layout live in the nested files; the skill points to `python.md` instead of copying it.

## Key facts learned

- Settings are **not inherited** from parent folders. Rules/hooks/deny live in the root `.claude/settings.json`, so start Claude at the root.
- Nested CLAUDE.md and path-scoped rules load **on demand** into message history: not in `/context` Memory files, summarized away on compaction until a matching file is touched again. See `notes/02-rules/paths-frontmatter.md`.
- If a rule must be active from the start, drop `paths:` or move it to the root file (costs every request). If it must hold every time, use a hook.
- Deny patterns anchor at the start folder: `Read(./.env)` misses `backend/.env`. Use `Read(./**/.env)`. Keep `.env.example` readable (`./**/.env.local` instead of `./**/.env.*`).
- Deny rules cover Claude's file tools and recognized Bash file commands, not subprocesses that open files themselves.
- A missing hook script is a non-blocking error (only exit 2 blocks), so commit the script. Mine exits quietly until `backend/pyproject.toml` exists.
- Frontend "tests" are `npm run lint` and `npm run build` until a test runner is added.

## Test results

| Check | Result |
|---|---|
| Root + user CLAUDE.md at launch; `backend/CLAUDE.md` and `python.md` on first backend read | Confirmed by a session run in `claude-demo/` |
| `settings.json` valid JSON; hook script exits 0 for `.js` and for `.py` before the project exists | Tested from here |
| `.gitignore` catches `CLAUDE.local.md`, `.env`, `backend/.env` | Tested with `git check-ignore` |
| `/hooks`, `/permissions`, `/add-endpoint` listed, `.env` read refused | You reported "everything working"; I didn't see the output |
| ruff actually formatting a file | **Not tested**: ruff not installed, backend project not scaffolded |

## Mistakes caught

- My expected test result was wrong: nested files are not listed under `/context` Memory files.
- My first deny rule `Read(./.env)` missed `backend/.env` and `frontend/.env`; fixed.

## Quiz (answered)

1. Why nested CLAUDE.md files: backend rules stay out of context during frontend work. ✅
2. Enforce "never edit `.env`": `permissions.deny` or a hook; CLAUDE.md is only a request. ✅
3. `backend/.claude/settings.json` read from the root? No, not inherited. ✅
4. Why `pytest` not in root file: Python lives only in `backend/`. ✅
5. Which fire on a backend edit: I named the rule and `backend/CLAUDE.md`, missed the ruff hook (runs after the edit). Half right.
6. `frontend/src/api/client.js` edit: hook does nothing, `react.md` loads. ✅
7. `cat .env`: covered; limits are subprocesses and `grep -r` over folders. ✅ (limits added by me)
8. Why the skill points to `python.md`: avoid duplicates. ✅
9. Why a skill, not a section in `backend/CLAUDE.md`: workflow; the cost/timing reason (loads only when used) was missing. Partly.

Sources: https://code.claude.com/docs/en/large-codebases, /context-window, /memory, /features-overview
