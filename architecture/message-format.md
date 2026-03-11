# MCP Message Format

All MCP communication uses **JSON-RPC 2.0** as the wire format. There are three types of messages.

## Request

A request expects a response from the other side.

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/call",
  "params": {
    "name": "search_files",
    "arguments": {
      "query": "hello world"
    }
  }
}
```

- `id` — Unique identifier for correlating responses
- `method` — The RPC method name
- `params` — Optional parameters object

## Response

A response is sent in reply to a request.

### Success Response
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "content": [
      { "type": "text", "text": "Found 3 matches..." }
    ]
  }
}
```

### Error Response
```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "error": {
    "code": -32602,
    "message": "Invalid params",
    "data": { "details": "Missing required field: query" }
  }
}
```

## Notification

A notification is a one-way message that does not expect a response.

```json
{
  "jsonrpc": "2.0",
  "method": "notifications/resources/updated",
  "params": {
    "uri": "file:///path/to/file.txt"
  }
}
```

## Standard Error Codes

| Code | Meaning |
|------|---------|
| `-32700` | Parse error |
| `-32600` | Invalid request |
| `-32601` | Method not found |
| `-32602` | Invalid params |
| `-32603` | Internal error |
