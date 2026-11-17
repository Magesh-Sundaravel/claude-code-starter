# MCP

**MCP (Model Context Protocol)** is an open standard that connects Claude Code to external tools, databases and APIs, so Claude acts on those systems directly instead of working from pasted data.

## Adding servers

| Transport | Use for | Example |
|---|---|---|
| `http` | Remote/cloud servers (recommended) | `claude mcp add --transport http notion https://mcp.notion.com/mcp` |
| `stdio` | Local process | `claude mcp add --transport stdio airtable -- npx -y airtable-mcp-server` |
| `sse` | Deprecated | Auto fallback on newer versions |

- `--` separates Claude's options from the server command.
- `claude mcp add-json <name> '<json>'` adds from a JSON string.
- Manage: `claude mcp list`, `claude mcp get <name>`, `claude mcp remove <name>`, `/mcp` in a session.

## Scopes

| Scope | Loads in | Shared | Stored in |
|---|---|---|---|
| Local (default) | This project | No | `~/.claude.json` |
| Project (`--scope project`) | This project | Yes, via git | `.mcp.json` in repo root |
| User (`--scope user`) | All projects | No | `~/.claude.json` |

Precedence: local > project > user > plugin servers > claude.ai connectors. Fields are **not merged**.

## `.mcp.json`

```json
{
  "mcpServers": {
    "api-server": {
      "type": "http",
      "url": "${API_BASE_URL:-https://api.example.com}/mcp",
      "headers": { "Authorization": "Bearer ${API_KEY}" }
    }
  }
}
```

| Env var syntax | Meaning |
|---|---|
| `${VAR}` | Value of `VAR` |
| `${VAR:-default}` | Default if unset (empty string counts as set) |
| Unset, no default | Config loads, Claude warns, `${VAR}` left literal |

- Expansion works in `command`, `args`, `env`, `url`, `headers`.
- Keeps secrets out of git: each person sets their own variable.
- For remote servers, credential vars like `ANTHROPIC_API_KEY` read as empty; copy into a variable with your own name.

## Approval

- Interactive sessions ask before using project-scoped servers from `.mcp.json`. A cloned repo can't approve its own servers until you trust the workspace.
- `claude mcp reset-project-choices` resets choices.
- `disabledMcpjsonServers` in settings blocks a server in every mode.
- `claude -p` / Agent SDK can't prompt, so project servers load without asking.

## Day to day

| Topic | What to know |
|---|---|
| `/mcp` | Server status, OAuth sign-in, toggle claude.ai connectors per project |
| Tool names | `mcp__<server>__<tool>` (double underscores). Names over 128 chars are left out; other tools still work |
| Tool search | Only tool names + server instructions load at start; full definitions load on demand |
| Output limits | Capped; tune with `MAX_MCP_OUTPUT_TOKENS` |
| Resources | Reference MCP resources in prompts |
| Per-tool approval | Can require approval for a specific tool |

## Hands-on

Saved `.mcp.json` in this repo with a fake `api-server` (`api.example.com`), not meant to connect. The approval prompt appears on the next interactive session.

## Quiz (answered)

1. Share a server with teammates via git: project scope, `.mcp.json`. ✅ (file not named at first)
2. Same server in local and user scope: local wins. ✅
3. Why `${API_KEY}` not the token: file is committed, secret stays out of git. ✅
4. Teammate clones: asks for approval first. ✅
5. Unset var, no default: config loads, warning, left unexpanded. ✅
6. Hands-on file: used `${...}` as a placeholder; fixed to `${API_KEY}`.
7. Full tool name for `create_issue` on `github`: `mcp__github__create_issue` (I wrote single underscores).
8. Why 10 servers don't fill context: tool search defers definitions. ✅
9. Check connection inside a session: `/mcp`. ✅

Source: https://code.claude.com/docs/en/mcp
