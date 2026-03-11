# Setting Up MCP with Claude Desktop

Claude Desktop is one of the primary host applications for MCP. This guide shows how to configure MCP servers.

## Configuration File Location

| OS | Path |
|----|------|
| macOS | `~/Library/Application Support/Claude/claude_desktop_config.json` |
| Windows | `%APPDATA%\Claude\claude_desktop_config.json` |
| Linux | `~/.config/Claude/claude_desktop_config.json` |

## Basic Configuration

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/home/user/documents"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_your_token_here"
      }
    }
  }
}
```

## Configuration Fields

| Field | Description |
|-------|-------------|
| `command` | The executable to run |
| `args` | Command-line arguments |
| `env` | Environment variables |

## Adding an MCP Server

1. Find or build an MCP server
2. Add its configuration to `claude_desktop_config.json`
3. Restart Claude Desktop
4. Look for the hammer icon in the chat input to verify

## Verifying Connection

When MCP servers are connected, Claude Desktop shows:
- A **hammer icon** indicating tool availability
- Tool count badge
- Server name in the tools panel

## Troubleshooting

- **Server not appearing**: Check JSON syntax, restart Claude Desktop
- **Permission errors**: Ensure the command is executable
- **Missing tools**: Check server logs via stderr
- **Connection timeout**: Verify the server starts within 30 seconds
