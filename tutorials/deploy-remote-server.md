# Deploying a Remote MCP Server

This guide covers deploying an MCP server as a remote HTTP service accessible over the network.

## Choosing Streamable HTTP Transport

Remote servers use Streamable HTTP transport instead of stdio.

## TypeScript — Express Server

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import {
  StreamableHTTPServerTransport
} from "@modelcontextprotocol/sdk/server/streamableHttp.js";
import express from "express";

const app = express();
app.use(express.json());

const server = new McpServer({
  name: "remote-server",
  version: "1.0.0"
});

// Register tools...
server.tool("ping", "Check server health", {}, async () => ({
  content: [{ type: "text", text: "pong" }]
}));

const transport = new StreamableHTTPServerTransport({ path: "/mcp" });

app.post("/mcp", transport.handlePost.bind(transport));
app.get("/mcp", transport.handleGet.bind(transport));
app.delete("/mcp", transport.handleDelete.bind(transport));

await server.connect(transport);

app.listen(3000, () => {
  console.log("MCP server running on http://localhost:3000/mcp");
});
```

## Python — FastMCP HTTP

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("remote-server")

@mcp.tool()
def ping() -> str:
    """Check server health."""
    return "pong"

# Run with HTTP transport
mcp.run(transport="streamable-http", host="0.0.0.0", port=3000)
```

## Deployment Options

| Platform | Notes |
|----------|-------|
| **Docker** | Containerize with Dockerfile |
| **Cloudflare Workers** | Edge deployment with built-in MCP support |
| **AWS Lambda** | Serverless with API Gateway |
| **Railway/Render** | Simple PaaS deployment |
| **VPS** | Direct deployment with systemd |

## Security Considerations

- Always use **HTTPS** in production
- Implement **OAuth 2.0** for authentication
- Set up **rate limiting**
- Use **CORS** headers appropriately
- Monitor and **log** all requests
