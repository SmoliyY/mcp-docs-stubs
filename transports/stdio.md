# stdio Transport

The **stdio** (Standard Input/Output) transport enables MCP communication over process streams.
It's the simplest and most performant transport option.

## How It Works

```
Host Application
    │
    ├── stdout ──→ MCP Server stdin
    │
    └── stdin  ←── MCP Server stdout
```

The host spawns the MCP server as a **child process**.
Messages flow through:
- **Client → Server**: Written to the server's `stdin`
- **Server → Client**: Written to the server's `stdout`

## Message Framing

Each JSON-RPC message is a single line of JSON, terminated by a newline character (`\n`).
Messages MUST NOT contain embedded newlines.

## stderr

The server's `stderr` is reserved for **logging and diagnostics**.
It is NOT used for protocol messages.
Hosts should capture stderr for debugging purposes.

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

## Unexpected exits

Since stdio servers run as child processes, hosts must handle cases where the process terminates unexpectedly (crashes, SIGKILL, etc.).

### Best practices

- **Monitor process state**: Listen for `exit` or `error` events on the child process.
- **Exponential backoff**: If a server crashes, avoid immediate tight-loop restarts.
  Implement exponential backoff (e.g., 1s, 2s, 4s, 8s) to prevent resource exhaustion.
- **Notify the user**: If a server fails repeatedly, inform the user rather than silently failing or infinitely retrying.
- **Cleanup resources**: Ensure that any pending requests are rejected with an appropriate error (e.g., "Connection closed") when the process exits.
- **Log stderr**: Capture and log the `stderr` output from the failed process, as it likely contains the reason for the crash.

For implementation examples, see [Error Recovery](../advanced/error-recovery.md).

## Implementation Notes

- Clients SHOULD support stdio whenever possible
- Servers MUST NOT write non-protocol data to stdout
- Both sides should handle process termination gracefully

## Shutdown

A client stops a stdio server by closing its stdin. The server exits once it reads end of file.
