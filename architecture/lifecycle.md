# MCP Connection Lifecycle

Every MCP connection follows a defined lifecycle with distinct phases.

## Phases

### 1. Initialization

The client sends an `initialize` request containing:
- Protocol version
- Client capabilities
- Client info (name, version)

```json
{
  "jsonrpc": "2.0",
  "method": "initialize",
  "params": {
    "protocolVersion": "2025-11-25",
    "capabilities": {
      "sampling": {},
      "roots": { "listChanged": true }
    },
    "clientInfo": {
      "name": "my-client",
      "version": "1.0.0"
    }
  },
  "id": 1
}
```

The server responds with its own capabilities and info.

### 2. Initialized Notification

After receiving the server's response, the client sends an `initialized` notification to confirm the handshake is complete.

```json
{
  "jsonrpc": "2.0",
  "method": "notifications/initialized"
}
```

### 3. Operation

The connection is now active. Client and server exchange messages:
- Client can call tools, read resources, list prompts
- Server can send notifications
- Server can request sampling from the client

### 4. Shutdown

Either side can terminate the connection. For stdio transport, closing the input stream signals shutdown. For HTTP transports, the session is explicitly closed.

## Error Handling

If initialization fails, the client should retry with exponential backoff. Servers must respond to `initialize` before accepting any other requests.
