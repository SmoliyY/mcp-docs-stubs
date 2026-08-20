# Cloudflare Workers

The Model Context Protocol (MCP) can be used to build servers that run on Cloudflare Workers, providing a scalable and edge-ready way to expose tools and resources to LLMs.

## Features

- **Fast** — Low latency edge execution.
- **Scalable** — Automatically handles traffic spikes.
- **Secure** — Runs in a sandboxed environment.

## Getting Started

To create an MCP server on Cloudflare Workers, you can use the `@modelcontextprotocol/sdk`.

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { SSEClientTransport } from "@modelcontextprotocol/sdk/client/sse.js";

// Example implementation
```

<!-- TODO: verify behaviour with product team -->
