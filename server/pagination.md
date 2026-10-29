# Pagination in MCP

MCP uses **cursor-based pagination** for listing tools, resources, prompts, and other collections.

## How It Works

When a server has more items than it wants to return in one response, it includes a `nextCursor` in the result. The client uses this cursor in subsequent requests to fetch the next page.

## Request

### First Page
```json
{
  "jsonrpc": "2.0",
  "method": "tools/list",
  "id": 1
}
```

### Subsequent Pages
```json
{
  "jsonrpc": "2.0",
  "method": "tools/list",
  "params": {
    "cursor": "eyJwYWdlIjogMn0="
  },
  "id": 2
}
```

## Response

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "tools": [
      { "name": "tool_1", "description": "..." },
      { "name": "tool_2", "description": "..." }
    ],
    "nextCursor": "eyJwYWdlIjogMn0="
  }
}
```

When there are no more pages, `nextCursor` is omitted or `null`.

## Paginated Methods

| Method | Returns |
|--------|---------|
| `tools/list` | Tools |
| `resources/list` | Resources |
| `resources/templates/list` | Resource templates |
| `prompts/list` | Prompts |

## Cursor Errors and Recovery

When a client provides an invalid or expired cursor in `params`, the server returns a JSON-RPC error response with code `-32602` (`Invalid params`):

```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "error": {
    "code": -32602,
    "message": "Invalid params",
    "data": {
      "details": "Invalid or expired cursor"
    }
  }
}
```

### Client Recovery

- **Do not retry the expired cursor**: An expired or invalid cursor does not recover.
  Clients must avoid retrying requests with the same cursor value.
- **Restart pagination**: The client should handle the error by requesting the first page again (omitting the `cursor` parameter), or by terminating the operation and reporting the error to the caller.

## Implementation Notes

- Cursors are **opaque strings** — clients should not parse them.
- Servers determine their own page size.
- Cursors may expire — servers return `-32602` (`Invalid params`), and clients should restart pagination from the first page rather than retrying the expired cursor.
- The cursor format is server-defined (often Base64-encoded JSON).

## TypeScript Example

```typescript
// Fetch all tools with pagination
let cursor: string | undefined;
const allTools = [];

do {
  const result = await client.listTools({ cursor });
  allTools.push(...result.tools);
  cursor = result.nextCursor;
} while (cursor);
```
