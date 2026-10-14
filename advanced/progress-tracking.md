# Progress Tracking

MCP supports progress reporting for long-running operations, giving users visibility into ongoing tasks.

## How It Works

1. Client includes a `progressToken` in the request metadata
2. Server sends `notifications/progress` with updates
3. Client displays progress to the user

## Initiating Progress Tracking

Client sends a tool call with a progress token:

```json
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "params": {
    "name": "import_data",
    "arguments": { "file": "large-dataset.csv" },
    "_meta": {
      "progressToken": "import-op-456"
    }
  },
  "id": 1
}
```

## Sending Progress Updates

Server sends periodic progress notifications:

```json
{
  "jsonrpc": "2.0",
  "method": "notifications/progress",
  "params": {
    "progressToken": "import-op-456",
    "progress": 250,
    "total": 1000,
    "message": "Importing row 250 of 1000"
  }
}
```

## Progress Fields

| Field | Type | Required | Description |
|-------|------|----------|-------------|
| `progressToken` | string/number | Yes | Matches the request's token |
| `progress` | number | Yes | Current progress value |
| `total` | number | No | Total expected value |
| `message` | string | No | Human-readable status |

## Request Cancellation

The core MCP specification does not provide a built-in mechanism for cancelling a standard request (like a tool call) once it has started, even if it is reporting progress.

### Standard Requests

While standard JSON-RPC 2.0 implementations sometimes use `$/cancel`, MCP does not formalize this notification for standard requests.
If a client needs to stop a long-running request:

- **Transport Termination**: Closing the underlying transport (e.g., the stdio stream) will stop the session, but this is a destructive action.
- **Wait for Completion**: The client can choose to ignore further progress notifications and wait for the server to return a final response or time out.

### Tasks API

For operations that require formal cancellation support, the experimental [Tasks API](tasks.md) is the recommended approach.
Tasks provide a dedicated `tasks/cancel` method:

```json
{
  "jsonrpc": "2.0",
  "method": "tasks/cancel",
  "params": { "id": "task-123" },
  "id": 3
}
```

## TypeScript Example

```typescript
server.tool("import_data", "Import a CSV file", {
  file: z.string()
}, async ({ file }, { meta }) => {
  const rows = await readCSV(file);
  const token = meta?.progressToken;

  for (let i = 0; i < rows.length; i++) {
    await processRow(rows[i]);
    if (token && i % 100 === 0) {
      await server.sendProgress(token, i, rows.length);
    }
  }

  return {
    content: [{ type: "text", text: `Imported ${rows.length} rows` }]
  };
});
```

## Notes

- Progress tokens are optional — servers must check if one was provided.
- Send updates at reasonable intervals (not every iteration).
- The `total` field may be unknown at the start.
