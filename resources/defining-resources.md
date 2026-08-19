# Define MCP resources

This guide covers how to create resources in MCP servers.

## TypeScript — Static Resource

```typescript
import fs from "node:fs/promises";
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
import fs from "node:fs/promises";
import { ResourceTemplate } from "@modelcontextprotocol/sdk/server/mcp.js";

server.resource(
  "source-file",
  new ResourceTemplate("file:///{path}", { list: undefined }),
  async (uri, { path }) => ({
    contents: [{
      uri: uri.href,
      mimeType: "text/plain",
      text: await fs.readFile(path, "utf-8")
    }]
  })
);
```

## TypeScript — Binary Content

For resources containing binary data, use the `blob` property in the `contents` array.
The binary content must be encoded as a base64 string.
You must also specify the appropriate `mimeType` for the content, such as `image/png` for images.

```typescript
import fs from "node:fs/promises";

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

## TypeScript — Resource parameters

The `server.resource()` method uses three main parameters to set up a resource:

{% table %}
* Parameter
* Type
* Description
---
* `name`
* `string`
* A unique name to identify this resource, such as `"readme"` or `"logo"`.
---
* `uriOrTemplate`
* `string | ResourceTemplate`
* Either a fixed URI string or a `ResourceTemplate` instance for dynamic resources.
---
* `handler`
* `Function`
* A function that gets and returns the resource content when it is requested.
{% /table %}

### The Resource Handler

The handler function receives the following arguments:

1.  **uri** (`URL`): The full URI of the requested resource.
2.  **variables** (`object`): An object containing values for any variables defined in the resource template.

The handler should return a `Promise` that resolves to an object containing a `contents` array.

## Best Practices

- Use descriptive resource names
- Set accurate MIME types
- Keep resources lightweight (fast reads)
- Implement subscriptions for dynamic content
- Use templates for parameterized data
