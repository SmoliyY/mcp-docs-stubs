# Transport Selection for Clients

Choosing the right transport is important for client implementations. This guide helps you decide.

## Decision Flow

```
Is the server local?
├── Yes → Use stdio
└── No  → Use Streamable HTTP
```

## stdio Transport

### When to Use
- Server runs as a child process
- Server is installed locally
- Single-user scenario

### TypeScript Setup
```typescript
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio.js";

const transport = new StdioClientTransport({
  command: "node",
  args: ["path/to/server.js"],
  env: { API_KEY: "..." }
});
```

### Process Management
The client manages the server's lifecycle:
- Spawns the process on connect
- Monitors for unexpected exits
- Kills the process on disconnect

## Streamable HTTP Transport

### When to Use
- Server is remote/cloud-hosted
- Multiple clients connect to one server
- Auth is required

### TypeScript Setup
```typescript
import {
  StreamableHTTPClientTransport
} from "@modelcontextprotocol/sdk/client/streamableHttp.js";

const transport = new StreamableHTTPClientTransport(
  new URL("https://api.example.com/mcp"),
  {
    headers: {
      Authorization: "Bearer token...",
      "X-Custom-Header": "value"
    }
  }
);
```

### Configuration Options

The `StreamableHTTPClientTransport` constructor accepts an optional configuration object to customize the connection behavior.

#### Custom Headers
The `headers` property allows you to pass any valid HTTP headers.
These headers are included in both the initial SSE connection request and subsequent POST requests.
Common uses include:
- **Authentication**: Passing Bearer tokens or API keys.
- **Custom Metadata**: Including client-specific identifiers or context.
- **Infrastructure**: Providing headers required by load balancers or reverse proxies.

#### Request Control
The transport supports additional configuration for managing the request lifecycle:
- **signal**: An `AbortSignal` instance that allows you to cancel the transport's connection and any ongoing requests.
<!-- TODO: verify if additional fetch-compatible options like 'credentials' or 'mode' are supported -->

## Fallback Strategy

Some clients implement fallback logic:

1. Try Streamable HTTP first
2. If it fails, try legacy SSE
3. If config has `command`, use stdio

```typescript
async function connectToServer(config) {
  if (config.url) {
    try {
      return await connectHTTP(config.url);
    } catch {
      return await connectSSE(config.url);
    }
  }
  return await connectStdio(config.command, config.args);
}
```

## Connection Health

Monitor connection health with periodic pings and handle disconnections gracefully with automatic reconnection logic.
