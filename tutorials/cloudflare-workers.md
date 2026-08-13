# MCP on Cloudflare Workers

Cloudflare Workers provide a serverless, edge-deployed platform for running MCP servers with built-in support.

## Why Cloudflare Workers?

- **Global edge deployment** — Low latency worldwide
- **Serverless** — No infrastructure to manage
- **Built-in MCP support** — Native Streamable HTTP transport
- **OAuth integration** — Authentication handled at the edge

## Getting Started

```bash
npm create cloudflare@latest -- my-mcp-server
cd my-mcp-server
npm install @modelcontextprotocol/sdk zod
```

## Worker Implementation

The following implementation demonstrates how to set up an MCP server within a Cloudflare Worker.
It uses the `@modelcontextprotocol/sdk` to define the server and its capabilities.
The server employs the `StreamableHTTPServerTransport` to handle communication over HTTP.

The implementation initializes the MCP server, defines tools, and sets up the transport to handle incoming requests at a specific path.
This example specifically shows a tool that interacts with Cloudflare KV storage.

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StreamableHTTPServerTransport } from "@modelcontextprotocol/sdk/server/streamableHttp.js";
import { z } from "zod";

export default {
  async fetch(request, env) {
    // Initialize the MCP server with basic metadata
    const server = new McpServer({
      name: "cloudflare-mcp",
      version: "1.0.0"
    });

    // Register a tool that can be called by clients.
    // In this example, it fetches data from a Cloudflare KV namespace.
    server.tool(
      "get_data",
      "Fetch data from KV store",
      { key: z.string() },
      async ({ key }) => {
        const value = await env.MY_KV.get(key);
        return {
          content: [{ type: "text", text: value || "Not found" }]
        };
      }
    );

    // Set up the Streamable HTTP transport.
    // Cloudflare Workers use this transport for HTTP-based communication.
    const transport = new StreamableHTTPServerTransport({ path: "/mcp" });

    // Connect the server to the transport
    await server.connect(transport);

    // Route requests to the MCP transport.
    // Ensure the request path matches the one defined in the transport.
    const url = new URL(request.url);
    if (url.pathname === "/mcp") {
      return transport.handle(request);
    }

    return new Response("MCP Server", { status: 200 });
  }
};
```

## Deploy

```bash
npx wrangler deploy
```

## Features Available on Workers

- **KV Storage** — Key-value data access
- **D1 Database** — SQL database queries
- **R2 Storage** — Object/file storage
- **AI** — Run AI models at the edge
- **Durable Objects** — Stateful sessions

## Configuration in Clients

```json
{
  "mcpServers": {
    "cloudflare": {
      "url": "https://my-mcp-server.workers.dev/mcp"
    }
  }
}
```
