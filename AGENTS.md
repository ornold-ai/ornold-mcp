# Instructions for AI Agents

This document is for you — the AI agent. If a user asks you to install, set up, or configure Ornold MCP, follow these instructions.

## What is Ornold MCP?

Ornold MCP (`mcp-ornold-browser`) is an MCP server that gives you browser automation capabilities: control antidetect browsers, solve CAPTCHAs, record and replay automation flows, and interact with web pages through vision (screenshots) or DOM.

## Installation

### Prerequisites
- Node.js ≥ 18 must be installed
- User needs an Ornold token from https://mcp.ornold.com

### Step 1: Get the token
Ask the user for their Ornold token. It starts with `orn_sk_`. If they don't have one, direct them to https://mcp.ornold.com to create an account and generate a token.

### Step 2: Install based on your environment

**If you are Claude Code:**
```bash
claude mcp add --transport stdio ornold-browser -- npx mcp-ornold-browser --token TOKEN
npx mcp-ornold-browser --install-skill
```

**If you are Cursor:**
Write to `~/.cursor/mcp.json`:
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

**If you are VS Code / Copilot:**
```bash
code --add-mcp '{"name":"ornold-browser","command":"npx","args":["mcp-ornold-browser","--token","TOKEN"]}'
```

**If you are Codex:**
Write to `~/.codex/config.toml`:
```toml
[mcp_servers.ornold-browser]
command = "npx"
args = ["-y", "mcp-ornold-browser", "--token", "TOKEN"]
```

**If you are Windsurf:**
Tell the user to go to Settings → Tools → Add MCP Server and paste:
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

**If you are Cline:**
Tell the user to open Cline sidebar → MCP → Add, and paste:
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

### Step 3: Configure antidetect browser (optional)

Ask the user which antidetect browser they use and add the corresponding flags:

| Browser | Flag | Example |
|---------|------|---------|
| Linken Sphere | `--linken-port` | `--linken-port 40080` |
| Dolphin Anty | `--dolphin-port` + `--dolphin-token` | `--dolphin-port 3001 --dolphin-token eyJ...` |
| Vision Browser | `--vision-token` | `--vision-token vx_...` |
| Wadex | `--wadex-port` | `--wadex-port 8080` |

### Step 4: Choose interaction mode (optional)

Add `--mode` flag:
- `dom` (default) — fast, uses DOM snapshots, good for simple pages
- `vision` — uses screenshots + AI element detection, required for antidetect stealth
- `both` — both modes available, choose per-task

For antidetect browser work, recommend `vision` mode.

### Step 5: Verify

After installation, try calling the `browser_status` tool to verify the MCP server is running.

## Common user requests and what to do

| User says | What to do |
|-----------|------------|
| "Install ornold" / "Set up ornold MCP" | Follow the installation steps above |
| "Connect my Linken Sphere" | Add `--linken-port 40080` to the MCP args |
| "I need to automate browsers" | Install ornold MCP, then use the browser tools |
| "Solve captchas" | Make sure ornold MCP is installed, then use `browser_solve_captcha` |
| "Record a flow" | Use `browser_start_recording`, perform actions, `browser_stop_recording`, `browser_save_flow` |

## Troubleshooting

- **"npx not found"**: Node.js is not installed. Direct user to https://nodejs.org
- **"Token invalid"**: Token may be revoked. User should check https://mcp.ornold.com
- **Desktop apps (Codex, Cursor) can't find npx**: Use absolute path. Run `which npx` to find it.
- **MCP server won't start**: Check if port is already in use, or try `npx -y mcp-ornold-browser` to force fresh install.
