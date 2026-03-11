# MCP Architecture Overview

MCP uses a **client-server architecture** with clearly defined roles and communication patterns.

## Architecture Diagram

```
┌─────────────────────────────────────┐
│           Host Application          │
│  (Claude Desktop, IDE, Custom App)  │
│                                     │
│  ┌──────────┐  ┌──────────┐        │
│  │ MCP      │  │ MCP      │  ...   │
│  │ Client A │  │ Client B │        │
│  └────┬─────┘  └────┬─────┘        │
└───────┼──────────────┼──────────────┘
        │              │
   ┌────▼─────┐   ┌────▼─────┐
   │ MCP      │   │ MCP      │
   │ Server A │   │ Server B │
   └────┬─────┘   └────┬─────┘
        │              │
   ┌────▼─────┐   ┌────▼─────┐
   │ Local    │   │ Remote   │
   │ Resource │   │ API      │
   └──────────┘   └──────────┘
```

## Layers

MCP consists of two primary layers:

### Data Layer
Defines the JSON-RPC protocol for client-server communication including:
- Lifecycle management (initialization, shutdown)
- Core primitives (tools, resources, prompts)
- Notifications and subscriptions

### Transport Layer
Defines communication mechanisms:
- **stdio** — Standard input/output for local processes
- **Streamable HTTP** — HTTP-based for remote servers
- **SSE** — Legacy, deprecated in favor of Streamable HTTP

## Key Principles

1. **1:1 Client-Server** — Each client connects to exactly one server
2. **Capability Negotiation** — Both sides declare supported features
3. **Stateful Sessions** — Connections maintain state across requests
4. **Bidirectional** — Both client and server can initiate messages
