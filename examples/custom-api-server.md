# Building a Custom API MCP Server

This example shows how to wrap any REST API as an MCP server, making it accessible to AI models.

## Pattern

```
AI Model → MCP Tool Call → Your Server → REST API → Response → AI Model
```

## TypeScript Example: Weather API

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { StdioServerTransport } from "@modelcontextprotocol/sdk/server/stdio.js";
import { z } from "zod";

const server = new McpServer({ name: "weather-api", version: "1.0.0" });

const API_KEY = process.env.WEATHER_API_KEY;
const BASE_URL = "https://api.openweathermap.org/data/2.5";

server.tool(
  "current_weather",
  "Get current weather conditions for a location",
  {
    city: z.string().describe("City name"),
    country: z.string().optional().describe("ISO country code")
  },
  async ({ city, country }) => {
    const q = country ? `${city},${country}` : city;
    const res = await fetch(
      `${BASE_URL}/weather?q=${q}&appid=${API_KEY}&units=metric`
    );

    if (!res.ok) {
      return {
        content: [{ type: "text", text: `API error: ${res.statusText}` }],
        isError: true
      };
    }

    const data = await res.json();
    return {
      content: [{
        type: "text",
        text: `${data.name}: ${data.main.temp}°C, ${data.weather[0].description}`
      }]
    };
  }
);

const transport = new StdioServerTransport();
await server.connect(transport);
```

## Python Example: REST API Wrapper

```python
from mcp.server.fastmcp import FastMCP
import httpx

mcp = FastMCP("api-wrapper")

@mcp.tool()
async def api_call(endpoint: str, method: str = "GET") -> str:
    """Make an API call to the configured service.

    Args:
        endpoint: API endpoint path (e.g., /users/123)
        method: HTTP method (GET, POST)
    """
    base_url = os.environ["API_BASE_URL"]
    api_key = os.environ["API_KEY"]

    async with httpx.AsyncClient() as client:
        response = await client.request(
            method, f"{base_url}{endpoint}",
            headers={"Authorization": f"Bearer {api_key}"}
        )
        return response.text
```

## Best Practices

- Validate and sanitize all inputs
- Rate-limit API calls
- Cache responses when appropriate
- Handle API errors gracefully
