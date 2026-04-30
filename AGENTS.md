# Instructions for AI Agents

You are an AI agent. A user has asked you to install Ornold MCP. Follow these steps.

## What is Ornold MCP?

Ornold MCP (`mcp-ornold-browser`) is an MCP server for browser automation: control antidetect browsers (Linken Sphere, Dolphin Anty, Vision Browser, Wadex), solve CAPTCHAs, record and replay automation flows, and interact with web pages through vision or DOM.

npm package: `mcp-ornold-browser`

## Prerequisites

- **Node.js ≥ 18** — check with `node -v`. If missing, tell the user to install from https://nodejs.org
- **Ornold token** — starts with `orn_sk_`. Ask the user for it, or tell them to get one at https://mcp.ornold.com (sign up → Tokens page → Create token)

## Installation by Agent Type

### Claude Code
```bash
claude mcp add --transport stdio ornold-browser -- npx mcp-ornold-browser --token TOKEN
npx mcp-ornold-browser --install-skill
```

### Cursor
Write to `~/.cursor/mcp.json` (create if missing, merge if exists):
```json
{
  "mcpServers": {
    "ornold-browser": {
      "command": "npx",
      "args": ["mcp-ornold-browser", "--token", "TOKEN"]
    }
  }
}
```
Tell user to restart Cursor.

### VS Code / Copilot
```bash
code --add-mcp '{"name":"ornold-browser","command":"npx","args":["mcp-ornold-browser","--token","TOKEN"]}'
```

### Codex
Write to `~/.codex/config.toml` (create if missing):
```toml
[mcp_servers.ornold-browser]
command = "npx"
args = ["-y", "mcp-ornold-browser", "--token", "TOKEN"]
```
Desktop apps don't inherit terminal PATH. If MCP fails, use absolute path from `which npx`.

### Windsurf
Tell user: Settings → Tools → Add MCP Server, paste:
```json
{
  "mcpServers": {
    "ornold-browser": {
      "command": "npx",
      "args": ["mcp-ornold-browser", "--token", "TOKEN"]
    }
  }
}
```

### Cline
Tell user: Cline sidebar → MCP → Add, paste:
```json
{
  "mcpServers": {
    "ornold-browser": {
      "command": "npx",
      "args": ["mcp-ornold-browser", "--token", "TOKEN"],
      "disabled": false
    }
  }
}
```

## Antidetect Browser Flags

Ask user which antidetect browser they use. Add flags to the args array:

| Browser | Flags |
|---------|-------|
| Linken Sphere | `"--linken-port", "40080"` |
| Dolphin Anty | `"--dolphin-port", "3001", "--dolphin-token", "API_TOKEN"` |
| Vision Browser | `"--vision-token", "X_TOKEN"` |
| Wadex | `"--wadex-port", "8080"` |

## Mode Selection

Add `"--mode", "MODE"` to args:
- `dom` (default) — fast, DOM-based
- `vision` — screenshot + AI detection, best for antidetect stealth
- `both` — both available

Recommend `vision` for antidetect browser work.

## Verify Installation

After setup, call the `browser_status` tool. If it responds, the MCP server is running correctly.

## Troubleshooting

| Problem | Solution |
|---------|----------|
| `npx: command not found` | Node.js not installed → https://nodejs.org |
| `Token invalid` | Token may be revoked → check https://mcp.ornold.com |
| MCP won't start in desktop app | Use absolute npx path: run `which npx`, use that in config |
| Connection timeout | Check if antidetect browser is running and port is correct |
