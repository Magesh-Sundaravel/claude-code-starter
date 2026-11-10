# Memory scopes (CLAUDE.md)

Claude Code loads **all** CLAUDE.md files it finds at startup and combines them.
If two conflict, the more specific one usually wins (Local > Project > User).
Managed is org policy and cannot be turned off.

| Scope   | Location (Linux)                         | Who it's for            | Commit to git?     |
|---------|------------------------------------------|-------------------------|--------------------|
| Managed | `/etc/claude-code/CLAUDE.md`             | Whole org, set by IT    | n/a                |
| User    | `~/.claude/CLAUDE.md`                    | Only me, every project  | No                 |
| Project | `./CLAUDE.md` or `./.claude/CLAUDE.md`   | My team, this repo      | Yes                |
| Local   | `./CLAUDE.local.md`                      | Only me, this repo      | No (gitignore it)  |

> Example files below are in code blocks on purpose. A real file named
> `CLAUDE.md` inside a subfolder would be loaded as memory.

---

## Managed: `/etc/claude-code/CLAUDE.md`

Use for company-wide rules nobody can override.

```markdown
# Acme Corp policy
- Never commit secrets or .env files.
- All code must pass the security scan before merge.
```

## User: `~/.claude/CLAUDE.md`

Use for personal preferences that apply to every project.

```markdown
# My preferences
- Reply in British English.
- Keep answers short.
- Prefer type hints in Python.
```

## Project: `./CLAUDE.md`

Use for team rules for this repo. Commit it.

```markdown
# my-api
- Use pytest, not unittest.
- Run tests: `uv run pytest`
- Backend is FastAPI, frontend is React + Vite.
```

## Local: `./CLAUDE.local.md`

Use for personal, repo-only notes. Add it to `.gitignore`.

```markdown
# My local setup
- My local DB credentials live in ~/secrets/my-api-db.env
- I run the backend on port 8001 (8000 is taken on my machine).
```

---

## Quick decision guide

- Applies to **all my projects**? Use **User**.
- The **team** should share it for this repo? Use **Project**.
- Only **me**, only **this repo**, must stay out of git? Use **Local**.
- The **company** requires it everywhere? Use **Managed**.

## Quiz (answered ✅)

1. British English in every project: **User**
2. "Use pytest, not unittest" for this repo, shared with the team: **Project**
3. Personal DB password path, this repo only, not in git: **Local**
4. User says "keep answers short", Local says "give detailed answers". Which wins? **Local**, because it's more specific than User.
