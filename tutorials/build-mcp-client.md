# Build an MCP Client

This tutorial shows how to build a simple MCP client that connects to servers and invokes tools.

## TypeScript Client
231231
```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio.js";

async function main() {
  // Create the client
  const client = new Client({
    name: "my-client",
    version: "1.0.0"
  });

  // Connect via stdio to a server
  const transport = new StdioClientTransport({
    command: "node",
    args: ["path/to/server.js"]
  });

  await client.connect(transport);

  // List available tools
  const { tools } = await client.listTools();
  console.log("Available tools:", tools.map(t => t.name));

  // Call a tool
  const result = await client.callTool("get_weather", {
    city: "London"
  });
  console.log("Result:", result.content);

  // List resources
  const { resources } = await client.listResources();
  console.log("Resources:", resources.map(r => r.uri));

  // Read a resource
  const resource = await client.readResource("weather://cities");
  console.log("Cities:", resource.contents);

  // List prompts
  const { prompts } = await client.listPrompts();
  console.log("Prompts:", prompts.map(p => p.name));

  // Get a prompt
  const prompt = await client.getPrompt("weather_report", {
    city: "Tokyo"
  });
  console.log("Prompt messages:", prompt.messages);

  // Disconnect
  await client.close();
}

main().catch(console.error);
```

## Key Client Methods

| Method | Description |
|--------|-------------|
| `listTools()` | Discover available tools |
| `callTool(name, args)` | Execute a tool |
| `listResources()` | Discover available resources |
| `readResource(uri)` | Read a resource |
| `listPrompts()` | Discover available prompts |
| `getPrompt(name, args)` | Get a prompt template |

## Error Handling

Always wrap tool calls in try/catch — servers may return errors or become unavailable.
