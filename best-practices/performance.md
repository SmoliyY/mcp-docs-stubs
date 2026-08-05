# MCP Performance Best Practices

Optimizing MCP server performance ensures a responsive user experience and efficient resource usage.

## Response Time Targets

| Operation | Target | Notes |
|-----------|--------|-------|
| `tools/list` | < 100ms | Cache the list |
| Simple tool call | < 1s | Most tools |
| Complex tool call | < 10s | Use progress reporting |
| Resource read | < 500ms | Cache when possible |
| Initialization | < 2s | Keep startup light |

## Caching Strategies

### Tool List Caching
```typescript
let cachedTools: Tool[] | null = null;

server.setRequestHandler("tools/list", async () => {
  if (!cachedTools) {
    cachedTools = await buildToolList();
  }
  return { tools: cachedTools };
});
```

### Resource Caching
```python
from functools import lru_cache

@lru_cache(maxsize=100)
def get_schema(table_name: str) -> str:
    """Cache database schemas."""
    return db.get_schema(table_name)
```

## Connection Pooling

For database servers, pool connections:

```typescript
import { Pool } from "pg";
const pool = new Pool({ max: 10 });

server.tool("query", "Run SQL", { sql: z.string() }, async ({ sql }) => {
  const client = await pool.connect();
  try {
    const result = await client.query(sql);
    return { content: [{ type: "text", text: JSON.stringify(result.rows) }] };
  } finally {
    client.release();
  }
});
```

## Large Response Handling

- **Truncate** large results with a summary.
- **Paginate** results instead of returning all items.
  Tools that return many items should implement their own pagination logic.
  Use cursor-based pagination for built-in `list` methods (tools, resources, and prompts) to avoid overloading clients.
  See the [Pagination guide](../server/pagination.md) for more information.
- **Stream** using progress notifications for long operations.
- **Compress** data where the format supports it.

### Custom tool pagination
Tools that return many items should implement their own pagination logic using tool arguments.
This allows the LLM to request data in manageable chunks.

```typescript
server.tool(
  "query_database",
  "Query the database for records",
  {
    sql: z.string(),
    cursor: z.string().optional(),
    limit: z.number().optional().default(100)
  },
  async ({ sql, cursor, limit }) => {
    const { records, nextCursor } = await db.execute(sql, { cursor, limit });

    return {
      content: [
        {
          type: "text",
          text: JSON.stringify({
            records,
            nextCursor,
            message: nextCursor 
              ? "More results available. Call again with the nextCursor." 
              : "End of results."
          }, null, 2)
        }
      ]
    };
  }
);
```

## Startup Optimization

- Lazy-load heavy dependencies
- Defer expensive initialization until first use
- Use connection pooling instead of per-request connections
- Pre-warm caches for frequently accessed data

## Memory Management

- Clean up resources after tool execution
- Set limits on result sizes
- Monitor memory usage in long-running servers
- Use streaming for large file operations
