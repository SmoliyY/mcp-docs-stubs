# Build Your First MCP Server (TypeScript)

This tutorial walks you through creating a simple MCP server in TypeScript that exposes a weather tool.

## Prerequisites

- Node.js 18+
- npm or yarn

## Step 1: Initialize the Project

```bash
mkdir my-mcp-server && cd my-mcp-server
npm init -y
npm install @modelcontextprotocol/sdk zod
npm install -D typescript @types/node
npx tsc --init
```

## Step 2: Create the Server

Create `src/index.ts`:

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({
  name: "weather-server",
  version: "1.0.0"
});

server.tool(
  "get_weather",
  "Get current weather for a city",
  {
    city: z.string().describe("City name"),
    units: z.enum(["celsius", "fahrenheit"]).default("celsius")
  },
  async ({ city, units }) => {
    // In a real server, call a weather API here
    return {
      content: [{
        type: "text",
        text: `Weather in ${city}: 22°${units === "celsius" ? "C" : "F"}, sunny`
      }]
    };
  }
);

async function main() {
  const transport = new StdioServerTransport();
  await server.connect(transport);
  console.error("Weather MCP server running on stdio");
}

main().catch(console.error);
```

## Step 3: Build and Run

```bash
npx tsc
node dist/index.js
```

## Step 4: Test with Claude Desktop

Add to `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "weather": {
      "command": "node",
      "args": ["/path/to/my-mcp-server/dist/index.js"]
    }
  }
}
```

Restart Claude Desktop. You can now ask: "What's the weather in Paris?"
