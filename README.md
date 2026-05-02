# Ornold MCP — Browser Automation for AI Agents

[![npm](https://img.shields.io/npm/v/mcp-ornold-browser)](https://www.npmjs.com/package/mcp-ornold-browser)

AI-powered browser automation with antidetect browser support, CAPTCHA solving, and flow recording.

**Works with:** Claude Code, Cursor, VS Code Copilot, Windsurf, Cline, Codex

## What It Does

- **Control antidetect browsers** — Linken Sphere, Dolphin Anty, Vision Browser, Wadex
- **Vision-first interaction** — screenshot → AI element detection → coordinate clicks (no CSS selectors)
- **CAPTCHA solving** — reCAPTCHA, hCaptcha, Cloudflare Turnstile, PerimeterX, and more
- **Flow recording & replay** — record once, replay across 100+ browsers in parallel
- **Human-like behavior** — Bezier mouse curves, gaussian typing delays, behavioral profiles

---

## 🚀 Install — Just Copy & Paste to Your Agent

Pick your agent, copy the message below, and paste it into the chat. The agent will do the rest.

### Claude Code

```
Install Ornold MCP — browser automation for antidetect browsers.

Requirements: Node.js ≥ 18 (https://nodejs.org)
Token: get one at https://mcp.ornold.com (sign up → Tokens page → Create token)

Run these two commands:
1. claude mcp add --transport stdio ornold-browser -- npx mcp-ornold-browser --token YOUR_TOKEN --mode vision
2. npx mcp-ornold-browser --install-skill

Replace YOUR_TOKEN with the token from the dashboard. The first command adds the MCP server (vision mode recommended for antidetect), the second installs the AI skill that teaches you how to use the browser tools effectively.

If you need both vision and DOM modes, create two separate servers:
1. claude mcp add --transport stdio ornold-vision -- npx mcp-ornold-browser --token YOUR_TOKEN --mode vision
2. claude mcp add --transport stdio ornold-dom -- npx mcp-ornold-browser --token YOUR_TOKEN --mode dom
3. npx mcp-ornold-browser --install-skill

After setup, verify by calling the browser_status tool.
```

### Cursor

```
Install Ornold MCP — browser automation for antidetect browsers.

Requirements: Node.js ≥ 18 (https://nodejs.org)
Token: get one at https://mcp.ornold.com (sign up → Tokens page → Create token)

Create or edit the file ~/.cursor/mcp.json and add this config:

{
  "mcpServers": {
    "ornold-browser": {
      "command": "npx",
      "args": ["mcp-ornold-browser", "--token", "YOUR_TOKEN", "--mode", "vision"]
    }
  }
}

Replace YOUR_TOKEN with the token from the dashboard. If the file already has other servers, merge the ornold-browser entry into the existing mcpServers object. Restart Cursor after saving.

For both modes, create two entries (ornold-vision with --mode vision, ornold-dom with --mode dom).
```

### VS Code (Copilot)

```
Install Ornold MCP — browser automation for antidetect browsers.

Requirements: Node.js ≥ 18 (https://nodejs.org)
Token: get one at https://mcp.ornold.com (sign up → Tokens page → Create token)

Run this command:
code --add-mcp '{"name":"ornold-browser","command":"npx","args":["mcp-ornold-browser","--token","YOUR_TOKEN","--mode","vision"]}'

Or create .vscode/mcp.json in the project root:

{
  "servers": {
    "ornold-browser": {
      "type": "stdio",
      "command": "npx",
      "args": ["mcp-ornold-browser", "--token", "YOUR_TOKEN", "--mode", "vision"]
    }
  }
}

Replace YOUR_TOKEN with the token from the dashboard.
```

### Windsurf

```
Install Ornold MCP — browser automation for antidetect browsers.

Requirements: Node.js ≥ 18 (https://nodejs.org)
Token: get one at https://mcp.ornold.com (sign up → Tokens page → Create token)

Go to Windsurf Settings → Tools → Add MCP Server and paste this config:

{
  "mcpServers": {
    "ornold-browser": {
      "command": "npx",
      "args": ["mcp-ornold-browser", "--token", "YOUR_TOKEN", "--mode", "vision"]
    }
  }
}

Replace YOUR_TOKEN with the token from the dashboard.
```

### Cline

```
Install Ornold MCP — browser automation for antidetect browsers.

Requirements: Node.js ≥ 18 (https://nodejs.org)
Token: get one at https://mcp.ornold.com (sign up → Tokens page → Create token)

Open Cline sidebar → MCP → Add and paste:

{
  "mcpServers": {
    "ornold-browser": {
      "command": "npx",
      "args": ["mcp-ornold-browser", "--token", "YOUR_TOKEN", "--mode", "vision"],
      "disabled": false
    }
  }
}

Replace YOUR_TOKEN with the token from the dashboard.
```

### Codex (CLI & Desktop)

```
Install Ornold MCP — browser automation for antidetect browsers.

Requirements: Node.js ≥ 18 (https://nodejs.org)
Token: get one at https://mcp.ornold.com (sign up → Tokens page → Create token)

Create or edit ~/.codex/config.toml and add:

[mcp_servers.ornold-browser]
command = "npx"
args = ["-y", "mcp-ornold-browser", "--token", "YOUR_TOKEN", "--mode", "vision"]

Replace YOUR_TOKEN with the token from the dashboard.

Note: Desktop apps don't inherit terminal PATH. If MCP fails to start, find your npx path with `which npx` and use the full path in the command field.
```

---

## Antidetect Browser Setup

After installing the MCP server, connect your antidetect browser by adding flags to the args:

| Browser | Add to args | Example |
|---------|------------|---------|
| **Linken Sphere** | `"--linken-port", "PORT"` | `"--linken-port", "40080"` |
| **Dolphin Anty** | `"--dolphin-port", "PORT", "--dolphin-token", "TOKEN"` | `"--dolphin-port", "3001", "--dolphin-token", "eyJ..."` |
| **Vision Browser** | `"--vision-token", "TOKEN"` | `"--vision-token", "vx_..."` |
| **Wadex** | `"--wadex-port", "PORT"` | `"--wadex-port", "8080"` |

### Interaction Modes

Add `"--mode", "MODE"` to args:

- **`dom`** — fast, uses DOM snapshots
- **`vision`** — screenshots + AI element detection, best for antidetect stealth
- **`both`** — both modes available (not recommended — loads too many tools, agent gets confused)

For antidetect browser automation, use `vision` mode.

### Best Practice: Separate MCP Servers

Create one MCP server per mode and per antidetect browser. This keeps the tool list small — the agent picks the right tool more reliably.

**Claude Code example (two modes + Linken Sphere):**
```bash
claude mcp add --transport stdio ornold-vision -- npx mcp-ornold-browser --token TOKEN --mode vision --linken-port 40080
claude mcp add --transport stdio ornold-dom -- npx mcp-ornold-browser --token TOKEN --mode dom --linken-port 40080
```

**Cursor example (two antidetect browsers):**
```json
{
  "mcpServers": {
    "ornold-linken": {
      "command": "npx",
      "args": ["mcp-ornold-browser", "--token", "TOKEN", "--mode", "vision", "--linken-port", "40080"]
    },
    "ornold-dolphin": {
      "command": "npx",
      "args": ["mcp-ornold-browser", "--token", "TOKEN", "--mode", "vision", "--dolphin-port", "3001", "--dolphin-token", "API_TOKEN"]
    }
  }
}
```

---

## Install Skills (Recommended)

Skills teach the AI agent how to use Ornold tools correctly. Without them, agents may use JavaScript injection instead of human-like character-by-character typing, or skip the vision workflow.

| Agent | Command |
|-------|---------|
| **Claude Code** | `npx mcp-ornold-browser --install-skill` |
| **Cursor** | Copy `skills/.cursorrules` to project root |
| **Windsurf** | Copy `skills/.windsurfrules` to project root |
| **Cline** | Copy `skills/.clinerules` to project root |
| **VS Code Copilot** | Copy `skills/copilot-instructions.md` to `.github/copilot-instructions.md` |
| **Codex** | Reads `AGENTS.md` automatically |

Full tool reference with examples: [`skills/ornold-browser.md`](skills/ornold-browser.md)

---

## Tool Examples

### Type text (human-like, character by character)
```
browser_parallel_type({selector: "[ref=3]", text: "user@mail.com"})
```
Each character is typed via CDP keyDown/char/keyUp with Shift for uppercase and symbols, gaussian delays between keystrokes, and per-browser typing profiles. Never use JavaScript to set input values.

### Vision mode — see and click
```
browser_parallel_vision_analyze_grouped()     → AI detects elements with [x1,y1,x2,y2] boxes
browser_parallel_click_normalized_box({box: [0.35, 0.72, 0.65, 0.78]})  → click with Bezier mouse
```

### Fill form with per-browser data
```
browser_parallel_fill_multi({
  selector: "input[name=email]",
  values: {"browser1": "user1@mail.com", "browser2": "user2@mail.com"}
})
```

### Solve CAPTCHA
```
browser_solve_captcha()   → detects type, solves, injects token — all automatic
```

### Record and replay a flow
```
browser_start_recording()
// ... perform actions ...
browser_stop_recording()
browser_edit_flow({editStep: 3, editFields: {text: "{{row.email}}"}})
browser_save_flow({name: "Registration"})
browser_run_flow({flowId: "fl_abc", dataset: [{email: "a@b.com"}]})
```

---

## Features

### Vision-First Interaction
Instead of fragile CSS selectors, Ornold uses screenshots + AI element detection. The agent sees the page visually and clicks by coordinates — just like a human would. This avoids bot detection triggers that come from DOM manipulation.

### Flow Recording & Replay
Record a browser automation sequence once, then replay it across hundreds of profiles:
1. Start recording
2. Perform actions (navigate, click, type, solve captchas)
3. Save the flow with template variables (`{{row.email}}`, `{{row.password}}`)
4. Run with a dataset — each browser gets unique data

### Parallel Execution
All operations run across all connected browser profiles simultaneously. Start 50 sessions, navigate them all to different URLs, fill forms with unique data per profile.

### CAPTCHA Solving
Automatic detection and solving: reCAPTCHA v2/v3, hCaptcha, Cloudflare Turnstile, GeeTest, FunCaptcha, Amazon WAF, PerimeterX press-and-hold, and more.

### Human-Like Behavior
The server automatically generates:
- Bezier curve mouse movements
- Gaussian-distributed typing delays
- Per-browser behavioral profiles
- Randomized interaction patterns

No need to add artificial delays — it's all built in.

---

## Get Your Token

1. Go to [mcp.ornold.com](https://mcp.ornold.com)
2. Sign up or log in
3. Navigate to **Tokens** page
4. Create a new token
5. Copy and paste it into the config

---

## Links

- **Dashboard & Tokens**: [mcp.ornold.com](https://mcp.ornold.com)
- **Website**: [ornold.com](https://ornold.com)
- **npm**: [mcp-ornold-browser](https://www.npmjs.com/package/mcp-ornold-browser)

## License

MIT
