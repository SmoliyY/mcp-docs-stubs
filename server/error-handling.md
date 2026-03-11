# Error Handling in MCP Servers

Proper error handling ensures MCP servers are robust and provide useful feedback to clients and users.

## Error Types

### Protocol Errors (JSON-RPC)
Errors in the protocol itself — malformed requests, unknown methods, etc.

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "error": {
    "code": -32601,
    "message": "Method not found: tools/invalid"
  }
}
```

### Tool Errors
Errors during tool execution. These use the `isError` flag in the result:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "content": [
      { "type": "text", "text": "Database connection failed: timeout after 30s" }
    ],
    "isError": true
  }
}
```

## Standard Error Codes

| Code | Name | Description |
|------|------|-------------|
| `-32700` | Parse error | Invalid JSON |
| `-32600` | Invalid request | Not a valid JSON-RPC request |
| `-32601` | Method not found | Unknown method |
| `-32602` | Invalid params | Invalid method parameters |
| `-32603` | Internal error | Server internal error |

## TypeScript Error Handling

```typescript
server.tool("query_db", "Query the database", {
  sql: z.string()
}, async ({ sql }) => {
  try {
    const results = await db.query(sql);
    return { content: [{ type: "text", text: JSON.stringify(results) }] };
  } catch (error) {
    return {
      content: [{ type: "text", text: `Query failed: ${error.message}` }],
      isError: true
    };
  }
});
```

## Python Error Handling

```python
@mcp.tool()
def query_db(sql: str) -> str:
    """Execute a database query."""
    try:
        results = db.execute(sql)
        return json.dumps(results)
    except DatabaseError as e:
        raise McpError(f"Query failed: {e}")
```

## Best Practices

- Return descriptive error messages the LLM can understand
- Use `isError: true` for tool-level failures
- Log detailed errors server-side for debugging
- Never expose internal stack traces or credentials in error messages
