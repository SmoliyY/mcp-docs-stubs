# Notifications in MCP

Notifications are one-way messages that don't require a response. Both clients and servers can send notifications to inform the other side of events.

## Server → Client Notifications

### Resource Updated
```json
{
  "method": "notifications/resources/updated",
  "params": { "uri": "file:///data/config.json" }
}
```

### Resource List Changed
```json
{
  "method": "notifications/resources/list_changed"
}
```

### Tool List Changed
```json
{
  "method": "notifications/tools/list_changed"
}
```

### Prompt List Changed
```json
{
  "method": "notifications/prompts/list_changed"
}
```

### Log Message
```json
{
  "method": "notifications/message",
  "params": {
    "level": "info",
    "logger": "server",
    "data": "Operation completed successfully"
  }
}
```

### Progress
```json
{
  "method": "notifications/progress",
  "params": {
    "progressToken": "op-123",
    "progress": 75,
    "total": 100,
    "message": "Processing file 75 of 100"
  }
}
```

## Client → Server Notifications

### Initialized
```json
{
  "method": "notifications/initialized"
}
```

### Roots Changed
```json
{
  "method": "notifications/roots/list_changed"
}
```

## Progress Tracking

Servers can report progress for long-running operations. The client provides a `progressToken` in the request's `_meta` field:

```json
{
  "method": "tools/call",
  "params": {
    "name": "process_files",
    "_meta": { "progressToken": "op-123" }
  }
}
```

The server sends progress notifications using this token.

## Key Rules

- Notifications MUST NOT include an `id` field
- The receiver MUST NOT send a response to a notification
- Both sides should handle unknown notification methods gracefully
