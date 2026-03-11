# MCP Transports

Transports define **how** MCP clients and servers communicate. The transport layer handles the physical delivery of JSON-RPC messages.

## Available Transports

| Transport | Use Case | Status |
|-----------|----------|--------|
| **stdio** | Local process communication | Recommended |
| **Streamable HTTP** | Remote server communication | Recommended |
| **HTTP+SSE** | Legacy remote communication | Deprecated |

## Choosing a Transport

### Use stdio when:
- Server runs as a **local subprocess**
- You need **maximum performance** (no network overhead)
- Server and client are on the **same machine**
- You want **simple deployment** (just run a binary)

### Use Streamable HTTP when:
- Server runs as a **remote service**
- **Multiple clients** need to connect to the same server
- You need **HTTP-based authentication**
- Server needs to be **horizontally scalable**

## Transport Requirements

All transports must:
1. Send and receive **JSON-RPC 2.0** messages
2. Handle message **framing** (separating individual messages)
3. Support **bidirectional** communication
4. Deliver messages **reliably** and in order

## Custom Transports

The protocol allows custom transport implementations. Any mechanism that can carry JSON-RPC messages bidirectionally can serve as an MCP transport. Examples include WebSockets, Unix domain sockets, or custom IPC mechanisms.
