# MCP Connection Lifecycle

Every MCP connection follows a defined lifecycle with distinct phases.

## Phases

### 1. Initialization

The client initiates the connection by sending an `initialize` request.
This request establishes the protocol version and exchanges capabilities.

#### Protocol Version Negotiation

The client sends its preferred `protocolVersion` in the request.

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

**Server behavior:**

- The server compares the client's requested `protocolVersion` against its supported versions.
- If the server supports the requested version, it responds with that version.
- If the server does not support the requested version, it SHOULD respond with the highest protocol version it supports that is compatible with the client's request.

<!-- TODO: verify if the server should return a specific error code if no compatible version is found -->

**Client behavior:**

- Upon receiving the server's response, the client MUST check the `protocolVersion` returned by the server.
- If the client cannot support the version returned by the server, it MUST NOT send the `initialized` notification and MUST instead close the connection.

### 2. Initialized Notification

After receiving a successful `initialize` response and confirming version compatibility, the client sends an `initialized` notification.
This signals to the server that the client is ready to begin normal operations.

```json
{
  "jsonrpc": "2.0",
  "method": "notifications/initialized"
}
```

The server MUST NOT send any requests or notifications (other than the initialization response) until it has received this notification.

### 3. Operation

The connection is now active.
Client and server exchange messages:

- Client can call tools, read resources, and list prompts.
- Server can send notifications (e.g., `notifications/resources/updated`).
- Server can request sampling or elicitation from the client.

### 4. Shutdown

Either side can terminate the connection.
Shutdown is primarily handled at the transport level rather than through a specific JSON-RPC method.

#### stdio transport

- **Client-initiated:** The client closes the server's `stdin` stream.
The server process should detect the end-of-file (EOF) and terminate gracefully.
- **Server-initiated:** The server closes its `stdout` stream or terminates its own process.

#### Streamable HTTP transport

- **Session termination:** The client sends an HTTP `DELETE` request to the server's MCP endpoint (e.g., `/mcp`) including the `Mcp-Session-Id` header.
- **Timeout:** Servers should implement a session timeout.
If no requests are received within the timeout period, the server may terminate the session and reclaim resources.

<!-- TODO: verify specific default timeout values with product team -->

## Error handling

If initialization fails, the error handling strategy depends on the failure type:

- **Transport errors:**
  For failures such as connection timeouts or network disruptions, the client should retry the initialization process with exponential backoff.
- **Protocol version mismatch:**
  If the server returns a protocol version that the client does not support, the client MUST close the connection.
  The client should not retry with the same version.

Servers must respond to `initialize` before accepting any other protocol requests.
Requests received before initialization should be rejected with an appropriate JSON-RPC error.
