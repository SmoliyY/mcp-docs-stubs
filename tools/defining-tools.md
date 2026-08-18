# Defining MCP Tools

This guide covers how to define tools in MCP servers using both TypeScript and Python SDKs.

## TypeScript (Official SDK)

The `server.tool` method is the primary way to register tools in the TypeScript SDK.

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { z } from "zod";

const server = new McpServer({
  name: "my-server",
  version: "1.0.0"
});

server.tool(
  "search_files", // Name of the tool
  "Search for files matching a pattern", // Description for the LLM
  {
    // Input schema (Zod)
    query: z.string().describe("Search query"),
    path: z.string().optional().describe("Directory to search in")
  },
  async ({ query, path }) => {
    // Implementation handler
    const results = await performSearch(query, path);
    return {
      content: [{ type: "text", text: JSON.stringify(results) }]
    };
  }
);
```

### Positional Arguments

The `server.tool` method takes four positional arguments:

| Argument | Type | Description |
|----------|------|-------------|
| **Name** | `string` | A unique name for the tool (e.g., `"search_files"`). |
| **Description** | `string` | A human-readable description explaining when and how the LLM should use this tool. |
| **Schema** | `object` | A Zod shape defining the tool's input parameters. This is automatically converted to JSON Schema. |
| **Handler** | `function` | An asynchronous function that executes the tool logic with validated inputs. |

## Python (FastMCP)

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("my-server")

@mcp.tool()
def search_files(query: str, path: str = ".") -> str:
    """Search for files matching a pattern."""
    results = perform_search(query, path)
    return str(results)
```

## Best Practices for Tool Definitions

1. **Write clear descriptions** — The LLM uses these to decide when to call the tool
2. **Use descriptive parameter names** — Self-documenting is better
3. **Mark optional parameters** — Don't require what isn't needed
4. **Add parameter descriptions** — Explain expected formats and constraints
5. **Validate inputs** — Check parameters before processing
6. **Return structured data** — JSON is preferred over plain text

## Input Schema Rules

- Must be a valid JSON Schema object
- `type` must be `"object"`
- Top-level `properties` define the parameters
- Use `required` array to specify mandatory parameters
