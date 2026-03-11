# Building an MCP Client

An MCP client connects to servers, discovers capabilities, and routes tool calls from the LLM. This guide covers client architecture and implementation.

## Client Responsibilities

1. **Connect** to MCP servers via transports
2. **Negotiate** capabilities during initialization
3. **Discover** available tools, resources, and prompts
4. **Route** LLM tool calls to the appropriate server
5. **Display** results back to the user or LLM
6. **Handle** notifications from servers

## Architecture

```
User / LLM
    │
    ▼
┌─────────────┐
│  MCP Client │
│             │
│ ┌─────────┐ │     ┌──────────┐
│ │Session A│─┼────►│ Server A │
│ └─────────┘ │     └──────────┘
│             │
│ ┌─────────┐ │     ┌──────────┐
│ │Session B│─┼────►│ Server B │
│ └─────────┘ │     └──────────┘
└─────────────┘
```

## TypeScript Implementation

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio.js";

const client = new Client({
  name: "my-host",
  version: "1.0.0"
}, {
  capabilities: {
    sampling: {},
    roots: { listChanged: true }
  }
});

const transport = new StdioClientTransport({
  command: "node",
  args: ["server.js"]
});

await client.connect(transport);
```

## Multi-Server Management

Most hosts manage multiple MCP clients — one per server:

```typescript
const servers = config.mcpServers;
const clients = new Map();

for (const [name, serverConfig] of Object.entries(servers)) {
  const client = new Client({ name: "host", version: "1.0.0" });
  const transport = new StdioClientTransport(serverConfig);
  await client.connect(transport);
  clients.set(name, client);
}
```

## Tool Aggregation

Collect tools from all servers and present them to the LLM as a unified set. Handle name conflicts by prefixing with the server name.
