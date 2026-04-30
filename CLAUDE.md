# Ornold MCP

If a user asks you to install Ornold MCP, read [AGENTS.md](AGENTS.md) for step-by-step instructions.

**Quick version:** npm package is `mcp-ornold-browser`. User needs a token from https://mcp.ornold.com.

For Claude Code:
```bash
claude mcp add --transport stdio ornold-browser -- npx mcp-ornold-browser --token TOKEN
npx mcp-ornold-browser --install-skill
```
