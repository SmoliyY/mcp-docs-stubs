# MCP Server Configuration

This document covers how MCP servers are configured in different host applications.

## Configuration Format

Most MCP hosts use a JSON configuration file with a standard structure:

```json
{
  "mcpServers": {
    "<server-name>": {
      "command": "<executable>",
      "args": ["<arg1>", "<arg2>"],
      "env": {
        "KEY": "value"
      }
    }
  }
}
```

## Configuration by Host

### Claude Desktop
```
~/Library/Application Support/Claude/claude_desktop_config.json
```

### Claude Code (CLI)
```
~/.claude/settings.json
```

### Cursor IDE
```
~/.cursor/mcp.json
```

### VS Code (Continue)
```
~/.continue/config.json
```

## Environment Variables

Environment variables let you pass secrets without hardcoding:

```json
{
  "mcpServers": {
    "database": {
      "command": "npx",
      "args": ["-y", "mcp-server-postgres"],
      "env": {
        "DATABASE_URL": "postgres://user:pass@localhost/db"
      }
    }
  }
}
```

## Remote Server Configuration

For HTTP-based servers, the configuration differs:

```json
{
  "mcpServers": {
    "remote-api": {
      "url": "https://api.example.com/mcp",
      "headers": {
        "Authorization": "Bearer token123"
      }
    }
  }
}
```

## Multiple Servers

You can configure multiple servers — each runs as an independent process:

```json
{
  "mcpServers": {
    "filesystem": { "command": "...", "args": ["..."] },
    "github": { "command": "...", "args": ["..."] },
    "database": { "command": "...", "args": ["..."] }
  }
}
```

Each server gets its own MCP client connection within the host.
