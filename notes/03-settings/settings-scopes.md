# User / project / local `settings.json`

Settings are JSON config that Claude Code **enforces**. Which file a key lives in decides who it affects.

## The files

| Scope | File | Applies to | Git |
|---|---|---|---|
| User | `~/.claude/settings.json` | You, every project | n/a |
| Shared project | `.claude/settings.json` | Everyone in the repo | Commit it |
| Project local | `.claude/settings.local.json` | You, this project only | Kept out of git (add to `.gitignore` if created by hand) |
| Managed | `managed-settings.json`, MDM, server-managed | Whole organization | Not yours to edit |

## Precedence (highest wins)

**Managed → command line (`--settings`) → local → shared project → user**

- A key set at a higher level overrides the same key lower down.
- **List keys merge** (e.g. `permissions.allow` from user + project are combined).
- Env vars are not a level; per key. `ANTHROPIC_MODEL` beats the `model` key from any file.
- Memory trick: the closer a file is to you and this project, the higher it ranks.

## Handy facts

- `/status` shows which setting sources loaded. `claude doctor` lists rejected entries.
- Claude Code reloads edited settings files live, no restart needed for most keys.
- "Yes, and don't ask again" on a permission prompt writes an `allow` rule to `settings.local.json`.
- Strict JSON: no comments, no trailing commas.
- Some keys never apply from the shared file (e.g. `permissions.defaultMode: bypassPermissions`).
- Committed `permissions.allow` rules wait until each teammate trusts the folder.

## CLAUDE.md vs settings.json

| | CLAUDE.md | settings.json |
|---|---|---|
| Combine how | Concatenated | Override (lists merge) |
| Order | Managed → User → Project → Local (read last = more weight) | Managed → CLI → Local → Shared → User (highest wins) |
| Conflict | Claude may pick either | Highest level wins |
| Nature | Guidance (context) | Enforcement (config) |

Need a guarantee? Use settings.json `deny` rules or a hook, not CLAUDE.md.

## Quiz (answered)

1. User `sonnet`, shared project `opus`, local empty → **Opus** (shared beats user). My first answer was wrong, I had the order reversed.
2. User `allow: [ls]` + project `allow: [git status]` → **both** (lists merge). ✅
3. Local `haiku` over shared `opus` → local wins, only my sessions change. ✅
4. User CLAUDE.md "French" vs project CLAUDE.md "English" → **mostly English** (read later), but **not guaranteed**. ✅

Sources: https://code.claude.com/docs/en/settings, https://code.claude.com/docs/en/memory
