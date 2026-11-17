# What to commit vs gitignore

Project files are shared through git. Files in `~/.claude/` are personal and live outside the repo.

## Commit (shared)

| File | Why |
|---|---|
| `CLAUDE.md` | Team instructions |
| `.claude/rules/*.md` | Topic rules |
| `.claude/settings.json` | Shared permissions and hooks (enforced) |
| `.claude/skills/`, `agents/`, `commands/`, `output-styles/`, `workflows/` | Shared extensions |
| `.mcp.json` | Team MCP servers (secrets via `${VAR}`) |
| `.claude/hooks/*.sh` | Scripts the committed settings point to |
| `.worktreeinclude` | Gitignored files to copy into new worktrees |
| `.claude/agent-memory/` | Project subagent memory (marked committable) |

## Don't commit (personal)

| File | Why | Ignored how |
|---|---|---|
| `CLAUDE.local.md` | Private project preferences | You add it to `.gitignore` manually |
| `.claude/settings.local.json` | Personal overrides, ranks above `settings.json` | Claude Code gitignores it when it saves a setting there |
| `~/.claude/CLAUDE.md`, `settings.json`, `rules/`, `skills/`, `agents/` | User scope, all projects | Outside the repo |
| `~/.claude.json` | Account, trust decisions, personal MCP servers | Outside the repo |
| `~/.claude/projects/<project>/memory/` | Auto memory | Outside the repo |
| `~/.claude/plugins/` | Installed plugin files (why teammates must install) | Outside the repo |

## This repo

`.gitignore` = `CLAUDE.local.md` and `.claude/settings.local.json` (both verified with `git check-ignore`). Tracked: `.claude/settings.json`, `rules/`, `hooks/`, `.mcp.json`.

## Notes

- A committed hook script is required: if missing, the hook can't run. Only exit code 2 blocks, so a missing script is a non-blocking error and the guard silently doesn't protect teammates.
- Secrets: put `${API_KEY}` in `.mcp.json`; each person sets the variable in their own environment. The docs I read don't say `.env` files are loaded (unverified). If you use `.env`, gitignore it.
- Transcripts in `~/.claude/projects/` are plaintext. A `.env` Claude reads or a secret a command prints is written there. Use `Read` deny rules for credential files; `cleanupPeriodDays` shortens retention (default 30 days).

## Quiz (answered)

1. Commit among `CLAUDE.local.md`, `.claude/settings.json`, `.mcp.json`, `~/.claude/CLAUDE.md`: `settings.json` and `.mcp.json`. ✅
2. Who adds `CLAUDE.local.md` to `.gitignore`: you (or ask Claude). ✅
3. Commit the hook script? Yes. I gave "deterministic" as the reason; the real reason is the committed settings reference it. Right answer, different reason.
4. Token in `.mcp.json`: `${API_KEY}` plus each person's environment. I added `.env`, which isn't verified in the docs. Half right.
5. Auto memory: your machine only, not shared. ✅

Source: https://code.claude.com/docs/en/claude-directory
