# Tasks (Experimental)

Tasks are a new abstraction introduced in the **2025-11-25** specification for tracking asynchronous work performed by MCP servers.

## What Are Tasks?

Tasks represent long-running operations that may not complete immediately. Instead of blocking on a tool call, the server creates a task that the client can poll for status and results.

## Task Lifecycle

```
Created → Running → Completed
                  → Failed
                  → Cancelled
```

## Creating a Task

When a tool call initiates a long-running operation, the server returns a task reference:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "content": [{ "type": "text", "text": "Task started" }],
    "task": {
      "id": "task-123",
      "status": "running",
      "message": "Processing data..."
    }
  }
}
```

## Querying Task Status

```json
{
  "jsonrpc": "2.0",
  "method": "tasks/get",
  "params": {
    "id": "task-123"
  },
  "id": 2
}
```

Response:

```json
{
  "task": {
    "id": "task-123",
    "status": "completed",
    "message": "Processing complete",
    "result": {
      "content": [{ "type": "text", "text": "Processed 1000 records" }]
    }
  }
}
```

## Task Cancellation

```json
{
  "jsonrpc": "2.0",
  "method": "tasks/cancel",
  "params": { "id": "task-123" },
  "id": 3
}
```

## Use Cases

- **Data processing** — Large file imports or transformations
- **Report generation** — Complex analytics queries
- **Deployment operations** — Build and deploy pipelines
- **Batch operations** — Processing multiple items

## Note

Tasks are currently **experimental** and may change in future specification versions.
