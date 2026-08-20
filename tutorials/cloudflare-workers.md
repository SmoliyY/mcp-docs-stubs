# MCP on Cloudflare Workers

Cloudflare Workers provide a serverless, edge-deployed platform for running MCP servers with built-in support.

## Why Cloudflare Workers?

- **Global edge deployment** — Low latency worldwide
- **Serverless** — No infrastructure to manage
- **Built-in MCP support** — Native Streamable HTTP transport
- **OAuth integration** — Authentication handled at the edge

## Get Started

```bash
npm create cloudflare@latest -- my-mcp-server
cd my-mcp-server
npm install @modelcontextprotocol/sdk zod
```

## Worker Implementation

Implement your MCP server using `McpServer` and `StreamableHTTPServerTransport`.

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
    // Access Workers bindings from the context
    const value = await (env as any).MY_KV.get(key);
    return {
      content: [{ type: "text", text: value || "Not found" }]
    };
  }
);

const transport = new StreamableHTTPServerTransport({ path: "/mcp" });

// Connect the server to the transport
const connected = server.connect(transport);

export default {
  async fetch(request, env) {
    await connected;

    const url = new URL(request.url);
    if (url.pathname === "/mcp") {
      if (request.method === "POST") return transport.handlePost(request, { env });
      if (request.method === "GET") return transport.handleGet(request, { env });
    }
    return new Response("Not found", { status: 404 });
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
