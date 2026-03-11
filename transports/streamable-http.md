# Streamable HTTP Transport

**Streamable HTTP** is the modern transport for remote MCP servers, introduced in the 2025-06-18 specification to replace the deprecated HTTP+SSE transport.

## How It Works

The server exposes a single HTTP endpoint (e.g., `/mcp`). Communication uses:

- **POST requests** — Client sends messages to the server
- **GET requests** — Client opens an SSE stream for server-initiated messages
- **SSE responses** — Server can stream multiple messages in response to a POST

## Endpoints

### POST `/mcp`
Client sends JSON-RPC requests and notifications.

```http
POST /mcp HTTP/1.1
Content-Type: application/json

{"jsonrpc":"2.0","method":"tools/call","params":{"name":"search"},"id":1}
```

The server can respond with:
- A single JSON response (`Content-Type: application/json`)
- An SSE stream (`Content-Type: text/event-stream`) for multiple messages

### GET `/mcp`
Opens a persistent SSE connection for server-initiated messages (notifications, requests).

## Session Management

Servers can issue a session ID via the `Mcp-Session-Id` header:

```http
Mcp-Session-Id: abc123-def456
```

Clients include this header in subsequent requests to maintain session state.

## Advantages Over HTTP+SSE

- **Single endpoint** instead of separate SSE and POST endpoints
- **Stateless option** — Servers can work without sessions
- **Better scalability** — Easier to load-balance
- **Simpler client** — No need to manage separate SSE connection

## When to Use

Use Streamable HTTP for remote, multi-tenant, or cloud-hosted MCP servers.
