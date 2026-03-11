# Listing and Discovering Tools

Clients discover available tools by sending a `tools/list` request to the server.

## Request

```json
{
  "jsonrpc": "2.0",
  "method": "tools/list",
  "id": 1
}
```

### With Pagination

For servers with many tools, pagination is supported via cursors:

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
      {
        "name": "read_file",
        "description": "Read the contents of a file",
        "inputSchema": {
          "type": "object",
          "properties": {
            "path": { "type": "string", "description": "File path" }
          },
          "required": ["path"]
        }
      },
      {
        "name": "write_file",
        "description": "Write content to a file",
        "inputSchema": {
          "type": "object",
          "properties": {
            "path": { "type": "string" },
            "content": { "type": "string" }
          },
          "required": ["path", "content"]
        }
      }
    ],
    "nextCursor": "eyJwYWdlIjogMn0="
  }
}
```

## Change Notifications

If the server declared `tools: { listChanged: true }` during initialization, it will send a notification when tools change:

```json
{
  "jsonrpc": "2.0",
  "method": "notifications/tools/list_changed"
}
```

The client should re-fetch the tool list when receiving this notification.
