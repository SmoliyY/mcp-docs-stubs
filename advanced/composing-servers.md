# Composing MCP Servers

Server composition allows you to build complex MCP servers from smaller, reusable components.

## Patterns

### Server Wrapping
Create a new server that delegates to other servers:

```typescript
const mainServer = new McpServer({ name: "composed", version: "1.0.0" });

// Import tools from other modules
import { registerFileTools } from "./tools/files";
import { registerSearchTools } from "./tools/search";
import { registerGitTools } from "./tools/git";

registerFileTools(mainServer);
registerSearchTools(mainServer);
registerGitTools(mainServer);
```

### Modular Tool Registration

```typescript
// tools/files.ts
export function registerFileTools(server: McpServer) {
  server.tool("read_file", "Read a file", { path: z.string() },
    async ({ path }) => ({ content: [{ type: "text", text: await readFile(path) }] })
  );

  server.tool("write_file", "Write a file",
    { path: z.string(), content: z.string() },
    async ({ path, content }) => {
      await writeFile(path, content);
      return { content: [{ type: "text", text: "File written" }] };
    }
  );
}
```

### Python Modular Composition

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("composed-server")

# Import tool modules
from tools.files import register as register_files
from tools.db import register as register_db

register_files(mcp)
register_db(mcp)
```

```python
# tools/files.py
def register(mcp):
    @mcp.tool()
    def read_file(path: str) -> str:
        """Read a file."""
        return open(path).read()
```

## Dynamic Tool Registration

Add or remove tools at runtime:

```typescript
// Tools can be added after server initialization
function addTool(name: string, handler: Function) {
  server.tool(name, `Dynamic tool: ${name}`, schema, handler);
  // Notify clients of the change
}
```

## When to Compose

- **Monorepo tools** — Combine related tools in one server
- **Plugin systems** — Dynamically load tool modules
- **Feature flags** — Enable/disable tool groups
- **Domain servers** — Group tools by business domain
