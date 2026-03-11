# TypeScript SDK

The official TypeScript SDK for MCP provides both server and client libraries with full type safety.

## Installation

```bash
npm install @modelcontextprotocol/sdk zod
```

## Quick Start — Server

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
  name: "my-server",
  version: "1.0.0"
});

// Define a tool
server.tool(
  "hello",
  "Say hello to someone",
  { name: z.string().describe("Person's name") },
  async ({ name }) => ({
    content: [{ type: "text", text: `Hello, ${name}!` }]
  })
);

// Define a resource
server.resource(
  "greeting",
  "greeting://default",
  async (uri) => ({
    contents: [{
      uri: uri.href,
      mimeType: "text/plain",
      text: "Welcome to my MCP server!"
    }]
  })
);

// Start with stdio transport
const transport = new StdioServerTransport();
await server.connect(transport);
```

## Key Modules

| Module | Purpose |
|--------|---------|
| `server/mcp.js` | High-level server class |
| `server/stdio.js` | stdio transport |
| `server/streamableHttp.js` | HTTP transport |
| `client/index.js` | MCP client |
| `types.js` | Protocol type definitions |

## Zod Integration

The SDK uses **Zod** for schema definition. Tool input schemas are defined as Zod objects, which are automatically converted to JSON Schema.

## Repository

`github.com/modelcontextprotocol/typescript-sdk`
