# Setting Up MCP with Cursor IDE

Cursor is an AI-powered code editor with built-in MCP support. This guide shows how to configure MCP servers.

## Configuration File

Create or edit `~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/home/user/projects"]
    },
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_your_token"
      }
    }
  }
}
```

## Project-Level Configuration

For project-specific servers, create `.cursor/mcp.json` in your project root:

```json
{
  "mcpServers": {
    "project-db": {
      "command": "npx",
      "args": ["-y", "mcp-server-sqlite", "./data/app.db"]
    }
  }
}
```

## Verifying Connection

1. Open Cursor
2. Open the Command Palette (Cmd/Ctrl + Shift + P)
3. Search for "MCP" to see available commands
4. Check the MCP panel for connected servers

## Using MCP Tools in Cursor

Once configured, Cursor's AI assistant can:
- Use filesystem tools to read and modify project files
- Query databases through database tools
- Interact with GitHub for issue/PR management
- Access any tools provided by configured servers

## Common Servers for Development

| Server | Purpose |
|--------|---------|
| Filesystem | Read/write project files |
| GitHub | Repository management |
| SQLite/Postgres | Database access |
| Docker | Container management |
| Brave Search | Web research |

## Troubleshooting

- **Servers not loading**: Restart Cursor after config changes
- **Permission errors**: Check that commands are in PATH
- **npm errors**: Ensure Node.js is installed globally
