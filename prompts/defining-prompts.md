# Defining MCP Prompts

Learn how to create prompt templates in MCP servers using TypeScript and Python.

## TypeScript

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";
import { z } from "zod";

const server = new McpServer({ name: "my-server", version: "1.0.0" });

server.prompt(
  "summarize",
  "Summarize the provided text",
  { maxLength: z.string().optional().describe("Maximum summary length") },
  ({ maxLength }) => ({
    messages: [
      {
        role: "user",
        content: {
          type: "text",
          text: `Summarize the following text${maxLength ? ` in ${maxLength} words or less` : ""}:`
        }
      }
    ]
  })
);
```

## Python

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("my-server")

@mcp.prompt()
def summarize(max_length: str = "") -> str:
    """Summarize the provided text."""
    constraint = f" in {max_length} words or less" if max_length else ""
    return f"Summarize the following text{constraint}:"
```

## Embedding Resources in Prompts

Prompts can include resource content, combining templates with data:

```typescript
server.prompt(
  "review_file",
  "Review a specific file",
  { path: z.string().describe("File path to review") },
  async ({ path }) => ({
    messages: [
      {
        role: "user",
        content: {
          type: "resource",
          resource: {
            uri: `file:///${path}`,
            mimeType: "text/plain",
            text: await readFile(path)
          }
        }
      },
      {
        role: "user",
        content: {
          type: "text",
          text: "Please review this file for potential issues."
        }
      }
    ]
  })
);
```

## Multi-Turn Prompts

Prompts can define multi-turn conversations with both `user` and `assistant` messages to set up context before the actual task.
