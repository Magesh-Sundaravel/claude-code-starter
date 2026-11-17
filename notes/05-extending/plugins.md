# Plugins

A **plugin** is a directory of components that Claude Code installs and loads as one unit. A manifest at `.claude-plugin/plugin.json` names it.

## What a plugin holds

| Component | Location in plugin |
|---|---|
| Skills | `skills/<name>/SKILL.md` |
| Agents | `agents/*.md` |
| Hooks | `hooks/hooks.json` |
| MCP servers | `.mcp.json` |

Skills and agents are namespaced by plugin name: `/<plugin>:<skill>` (e.g. `/team-tools:lint`). Hooks have no prefix.

## Plugin or standalone?

- Standalone skills, agents, hooks and MCP all work alone. Keep them while they serve only you or one project.
- Use a plugin to package several as one unit: share with teammates, reuse across projects, ship versioned releases.

## Marketplaces and install

- A marketplace is a catalog (repo with `.claude-plugin/marketplace.json`). Install by name: `name@marketplace`.
- Anthropic's official marketplace is added on first interactive start. Browse/install with `/plugin`.
- Install scopes:

| Scope | Enabled for | Stored via |
|---|---|---|
| User | You, all projects | User settings |
| Project | Everyone in repo | Committed `.claude/settings.json` |
| Local | You, this repo only | `.claude/settings.local.json` |

## Enabled vs installed

| Stage | Meaning | Where | In git? |
|---|---|---|---|
| Enabled (declared) | Note saying the plugin should be on | `.claude/settings.json` (`enabledPlugins`) | Yes |
| Installed | Plugin files on this machine | `~/.claude/plugins/` | No |

A teammate who clones a project-scope plugin gets the note, not the files. `/plugin` Errors tab shows `enabled in project settings but isn't installed here`. Fix:

```
claude plugin install <name>@<marketplace> --scope project
```

then `/reload-plugins` or a new session. Project-scope plugins also pass the workspace trust check first.

## Cost and trust

- An enabled plugin is in every session: names/descriptions of invocable skills and agents sit in context each turn; its hooks and MCP servers run.
- Everything it runs, it runs **as you** (user privileges), whatever the marketplace. Read the source before installing.
- Check footprint in `/plugin` before installing. Disable without uninstalling: `claude plugin disable`.

## Create and test

```
my-first-plugin/
├── .claude-plugin/
│   └── plugin.json      ← only this goes inside
├── skills/hello/SKILL.md
├── agents/
├── hooks/hooks.json
└── .mcp.json
```

```json
{ "name": "my-first-plugin", "description": "...", "version": "1.0.0", "author": { "name": "You" } }
```

Only `name` is required.

| Command | Purpose |
|---|---|
| `claude plugin validate ./path` | Check manifest and skill frontmatter |
| `claude --plugin-dir ./path` | Load for one session, no install |
| `/my-first-plugin:hello` | Run the skill |
| `/reload-plugins` | Apply edits mid-session |
| `/plugin` | Installed tab = components found; Errors tab = failures |

## Converting `.claude/` to a plugin

Copy `commands/`, `agents/`, `skills/` to the plugin root. Move the `hooks` object from settings into `hooks/hooks.json`. Test, then delete the originals. Skills don't collide (prefix) but **hooks run twice** while both exist.

## Common failures

- `skills/` inside `.claude-plugin/`: plugin loads, skills missing.
- `--plugin-dir` at a marketplace root: nothing loads, no error.

## Quiz (answered)

1. Plugin vs standalone for skill + hook + MCP shared with a team: plugin, packages them as one shippable unit. ✅
2. Run skill `lint` from plugin `team-tools`: `/team-tools:lint` (I wrote `team-tools /lint`, wrong).
3. Project scope committed, active for teammate? No: enabled in settings but must be installed on their machine (I said yes, wrong).
4. Third-party marketplace risk: plugin runs code as you (I said "security risk", too vague).
5. Teammate sees no command: not installed. Fix: `claude plugin install <name>@<marketplace> --scope project`. ✅ (reason right, command missed)
6. Only file allowed in `.claude-plugin/`: `plugin.json`. ✅
7. Loads but no skills: `skills/` inside `.claude-plugin/`. ✅
8. Original hooks left after converting: run twice. ✅

Hands-on build of `learn-plugin` skipped.

Sources: https://code.claude.com/docs/en/plugins, /plugins/loading, /plugins/create
