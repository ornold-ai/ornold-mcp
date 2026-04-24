# Ornold MCP — Browser Automation for AI Agents

[![npm](https://img.shields.io/npm/v/mcp-ornold-browser)](https://www.npmjs.com/package/mcp-ornold-browser)
[![smithery](https://img.shields.io/badge/smithery-ornold--browser-blue)](https://smithery.ai/server/mcp-ornold-browser)

AI-powered browser automation with antidetect browser support, CAPTCHA solving, and flow recording.

**Works with:** Claude Code, Cursor, VS Code Copilot, Windsurf, Cline, Codex

## What It Does

- **Control antidetect browsers** — Linken Sphere, Dolphin Anty, Vision Browser, Wadex
- **Vision-first interaction** — screenshot → AI element detection → coordinate clicks (no selectors)
- **CAPTCHA solving** — reCAPTCHA, hCaptcha, Cloudflare Turnstile, PerimeterX, and more
- **Flow recording & replay** — record once, replay across 100+ browsers in parallel
- **Human-like behavior** — Bezier mouse curves, gaussian typing delays, behavioral profiles

## Quick Install

> **Just tell your AI agent:**
>
> *"Install Ornold MCP browser automation. The npm package is `mcp-ornold-browser`. Get a token at https://mcp.ornold.com"*
>
> Your agent will handle the rest.

### Or install manually:

<details>
<summary><b>Claude Code</b></summary>

```bash
claude mcp add --transport stdio ornold-browser -- npx mcp-ornold-browser --token YOUR_TOKEN
```

Then install the AI skill (teaches your agent how to use the tools effectively):
```bash
npx mcp-ornold-browser --install-skill
```
</details>

<details>
<summary><b>Cursor</b></summary>

Add to `~/.cursor/mcp.json`:
```json
{
  "mcpServers": {
    "ornold-browser": {
      "command": "npx",
      "args": ["mcp-ornold-browser", "--token", "YOUR_TOKEN"]
    }
  }
}
```
</details>

<details>
<summary><b>VS Code (Copilot)</b></summary>

```bash
code --add-mcp '{"name":"ornold-browser","command":"npx","args":["mcp-ornold-browser","--token","YOUR_TOKEN"]}'
```

Or add to `.vscode/mcp.json`:
```json
{
  "servers": {
    "ornold-browser": {
      "type": "stdio",
      "command": "npx",
      "args": ["mcp-ornold-browser", "--token", "YOUR_TOKEN"]
    }
  }
}
```
</details>

<details>
<summary><b>Windsurf</b></summary>

Settings → Tools → Add MCP Server:
```json
{
  "mcpServers": {
    "ornold-browser": {
      "command": "npx",
      "args": ["mcp-ornold-browser", "--token", "YOUR_TOKEN"]
    }
  }
}
```
</details>

<details>
<summary><b>Cline</b></summary>

Cline sidebar → MCP → Add:
```json
{
  "mcpServers": {
    "ornold-browser": {
      "command": "npx",
      "args": ["mcp-ornold-browser", "--token", "YOUR_TOKEN"],
      "disabled": false
    }
  }
}
```
</details>

<details>
<summary><b>Codex (CLI & Desktop)</b></summary>

Add to `~/.codex/config.toml`:
```toml
[mcp_servers.ornold-browser]
command = "npx"
args = ["-y", "mcp-ornold-browser", "--token", "YOUR_TOKEN"]
```
</details>

## Get Your Token

1. Go to [mcp.ornold.com](https://mcp.ornold.com)
2. Sign up or log in
3. Navigate to **Tokens** page
4. Create a new token
5. Use it in the `--token` argument

## CLI Options

| Flag | Description | Default |
|------|-------------|---------|
| `--token` | Your API token (**required**) | — |
| `--mode` | Interaction mode: `dom`, `vision`, or `both` | `dom` |
| `--linken-port` | Linken Sphere API port | — |
| `--dolphin-port` | Dolphin Anty local port | — |
| `--dolphin-token` | Dolphin Anty cloud API token | — |
| `--wadex-port` | Wadex API port | — |
| `--vision-token` | Vision Browser X-Token | — |
| `--vision-port` | Vision Browser local port | — |

## Features Overview

### Vision Mode
Instead of fragile CSS selectors, Ornold uses screenshots + AI element detection. The agent sees the page visually and clicks by coordinates — just like a human would.

### Flow Recording
Record a browser automation sequence once, then replay it across hundreds of profiles:
1. Start recording
2. Perform actions (navigate, click, type, solve captchas)
3. Save the flow
4. Run it with a dataset — each browser gets unique data (email, password, etc.)

### Parallel Execution
All browser operations run across all connected profiles simultaneously. Start 50 Linken Sphere sessions, navigate them all to different URLs, fill forms with unique data per profile.

### CAPTCHA Solving
Automatic detection and solving: reCAPTCHA v2/v3, hCaptcha, Cloudflare Turnstile, GeeTest, FunCaptcha, Amazon WAF, PerimeterX press-and-hold, and more.

### Antidetect Integration
Native integration with popular antidetect browsers:
- **Linken Sphere** — full session management, proxy config, geo settings, warmup
- **Dolphin Anty** — profile management via local + cloud API
- **Vision Browser** — cloud-based profile management with folders, tags, statuses
- **Wadex** — session management

## Requirements

- Node.js ≥ 18
- An Ornold token ([get one here](https://mcp.ornold.com))
- An antidetect browser (Linken Sphere, Dolphin Anty, Vision Browser, or Wadex)

## Links

- **Dashboard & Tokens**: [mcp.ornold.com](https://mcp.ornold.com)
- **Website**: [ornold.app](https://ornold.app)
- **npm**: [mcp-ornold-browser](https://www.npmjs.com/package/mcp-ornold-browser)

## License

MIT
