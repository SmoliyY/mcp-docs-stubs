# HTTP+SSE Transport (Deprecated)

The original HTTP+SSE transport was part of the initial MCP specification (2024-11-05). It has been **deprecated** in favor of Streamable HTTP.

## How It Worked

The transport used two separate endpoints:

### SSE Endpoint (GET)
```
GET /sse
```
Client connects to receive server-sent events. The server sends an `endpoint` event with the URL for posting messages:

```
event: endpoint
data: /messages?sessionId=abc123
```

### Message Endpoint (POST)
```
POST /messages?sessionId=abc123
Content-Type: application/json

{"jsonrpc":"2.0","method":"tools/list","id":1}
```

Server responses are sent back through the SSE stream, not as HTTP responses.

## Why It Was Deprecated

1. **Two endpoints** — Complex to set up and manage
2. **Stateful requirement** — Always needed session tracking
3. **Load balancing issues** — SSE connection pinned to one server
4. **Reconnection complexity** — Handling SSE disconnects was error-prone
5. **Proxy challenges** — Some proxies don't handle SSE well

## Migration Guide

To migrate from HTTP+SSE to Streamable HTTP:

1. Replace the two endpoints with a single `/mcp` endpoint
2. Handle POST requests directly (return JSON or SSE)
3. Optionally support GET for server-initiated messages
4. Session management becomes optional (via `Mcp-Session-Id` header)

## Backward Compatibility

Some MCP servers still use HTTP+SSE. Clients may need to support both transports during the transition period.
