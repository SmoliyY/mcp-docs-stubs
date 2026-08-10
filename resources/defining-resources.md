# Defining MCP Resources

This guide covers how to create resources in MCP servers.

## TypeScript — Static Resource

```typescript
import { McpServer } from "@modelcontextprotocol/sdk/server/mcp.js";

const server = new McpServer({ name: "docs-server", version: "1.0.0" });

// Static resource with fixed URI
server.resource(
  "readme",
  "file:///project/README.md",
  async (uri) => ({
    contents: [{
      uri: uri.href,
      mimeType: "text/markdown",
      text: await fs.readFile("/project/README.md", "utf-8")
    }]
  })
);
```

## TypeScript — Dynamic Resource Template

```typescript
import { ResourceTemplate } from "@modelcontextprotocol/sdk/server/mcp.js";

server.resource(
  "source-file",
  new ResourceTemplate("file:///{path}", { list: undefined }),
  async (uri, { path }) => ({
    contents: [{
      uri: uri.href,
      mimeType: getMimeType(path),
      text: await fs.readFile(path, "utf-8")
    }]
  })
);
```

## TypeScript — Binary Content

```typescript
server.resource(
  "logo",
  "assets://logo.png",
  async (uri) => ({
    contents: [{
      uri: uri.href,
      mimeType: "image/png",
      blob: (await fs.readFile("logo.png")).toString("base64")
    }]
  })
);
```

## TypeScript Parameters

The `server.resource()` method accepts three primary arguments:

<Table>

| Parameter | Type | Description |
| :--- | :--- | :--- |
| `name` | `string` | A unique identifier for the resource, such as `"readme"` or `"logo"`. |
| `uri / template` | `string | ResourceTemplate` | The URI for the resource (e.g., `"assets://logo.png"`) or a `ResourceTemplate` for dynamic paths. |
| `handler` | `Function` | An async callback that fetches and returns the resource content. |

</Table>

## Python — Static Resource

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("docs-server")

@mcp.resource("file:///project/README.md")
def get_readme() -> str:
    """The project README file."""
    with open("/project/README.md") as f:
        return f.read()
```

## Python — Dynamic Resource

```python
@mcp.resource("db://tables/{table_name}/schema")
def get_table_schema(table_name: str) -> str:
    """Get the schema for a database table."""
    schema = db.get_schema(table_name)
    return json.dumps(schema)
```

## Best Practices

- Use descriptive resource names
- Set accurate MIME types
- Keep resources lightweight (fast reads)
- Implement subscriptions for dynamic content
- Use templates for parameterized data
