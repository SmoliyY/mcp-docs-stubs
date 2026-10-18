# MCP on Cloudflare Workers

Cloudflare Workers provide a serverless, edge-deployed platform for running MCP servers.

## Benefits of Cloudflare Workers

- **Global edge deployment** — Low latency worldwide
- **Serverless** — No infrastructure to manage
- **Streamable HTTP support** — Efficient bidirectional communication over HTTP
- **OAuth integration** — Authentication handled at the edge

## Get started

```bash
npm create cloudflare@latest -- my-mcp-server
cd my-mcp-server
npm install @modelcontextprotocol/sdk zod
```

## Worker implementation

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StreamableHTTPServerTransport } from "@modelcontextprotocol/sdk/server/streamableHttp.js";
import { z } from "zod";

const server = new McpServer({
  name: "cloudflare-mcp",
  version: "1.0.0"
});

server.tool(
  "get_data",
  "Fetch data from KV store",
  { key: z.string() },
  async ({ key }, { env }) => {
    // Access Workers environment via the context object
    const value = await env.MY_KV.get(key);
    return {
      content: [{ type: "text", text: value || "Not found" }]
    };
  }
);

// Define the transport outside the fetch handler to reuse it
let transport: StreamableHTTPServerTransport | null = null;

export default {
  async fetch(request, env) {
    if (!transport) {
      transport = new StreamableHTTPServerTransport({ path: "/mcp" });
      await server.connect(transport);
    }

    const url = new URL(request.url);
    if (url.pathname === "/mcp") {
      // Streamable HTTP handles GET (for SSE) and POST (for messages)
      if (request.method === "GET") {
        return transport.handleGet(request, { env });
      } else if (request.method === "POST") {
        return transport.handlePost(request, { env });
      }
    }

    return new Response("MCP Server Running", { status: 200 });
  }
};
```

## Deploy

```bash
npx wrangler deploy
```

## Workers features

- **KV Storage** — Key-value data access
- **D1 Database** — SQL database queries
- **R2 Storage** — Object/file storage
- **AI** — Run AI models at the edge
- **Durable Objects** — Stateful sessions

## Client configuration

```json
{
  "mcpServers": {
    "cloudflare": {
      "url": "https://my-mcp-server.workers.dev/mcp"
    }
  }
}
```
