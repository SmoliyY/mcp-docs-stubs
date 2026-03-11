# stdio Transport

The **stdio** (Standard Input/Output) transport enables MCP communication over process streams. It's the simplest and most performant transport option.

## How It Works

```
Host Application
    │
    ├── stdout ──→ MCP Server stdin
    │
    └── stdin  ←── MCP Server stdout
```

The host spawns the MCP server as a **child process**. Messages flow through:
- **Client → Server**: Written to the server's `stdin`
- **Server → Client**: Written to the server's `stdout`

## Message Framing

Each JSON-RPC message is a single line of JSON, terminated by a newline character (`\n`). Messages MUST NOT contain embedded newlines.

## stderr

The server's `stderr` is reserved for **logging and diagnostics**. It is NOT used for protocol messages. Hosts should capture stderr for debugging purposes.

## Configuration Example

In Claude Desktop's config:

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-filesystem", "/home/user/docs"],
      "env": {
        "NODE_ENV": "production"
      }
    }
  }
}
```

## Advantages

- **Zero network overhead** — Direct process communication
- **Simple setup** — No ports, no URLs, no certificates
- **Secure by default** — No network exposure
- **Cross-platform** — Works on all operating systems

## Limitations

- **Local only** — Both processes must be on the same machine
- **Single client** — One client per server process
- **Process lifecycle** — Server lifecycle tied to the client

## Implementation Notes

- Clients SHOULD support stdio whenever possible
- Servers MUST NOT write non-protocol data to stdout
- Both sides should handle process termination gracefully
