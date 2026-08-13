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

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StreamableHTTPServerTransport } from "@modelcontextprotocol/sdk/server/streamableHttp.js";
import { z } from "zod";

export default {
  async fetch(request, env) {
    const server = new McpServer({
      name: "cloudflare-mcp",
      version: "1.0.0"
    });

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

    const transport = new StreamableHTTPServerTransport({ path: "/mcp" });
    await server.connect(transport);

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
