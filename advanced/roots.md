# Roots

Roots tell MCP servers which **filesystem locations** or **URIs** they should focus on. They define the boundaries of what the server should operate on.

## What Are Roots?

Roots are URIs that a client provides to a server, indicating the relevant working directories or data sources. They help servers understand the user's workspace context.

## Prerequisites

The client declares root support:

```json
{
  "capabilities": {
    "roots": {
      "listChanged": true
    }
  }
}
```

## Listing Roots

Servers request roots from the client:

```json
{
  "jsonrpc": "2.0",
  "method": "roots/list",
  "id": 1
}
```

Response:

```json
{
  "roots": [
    {
      "uri": "file:///home/user/project-a",
      "name": "Project A"
    },
    {
      "uri": "file:///home/user/project-b",
      "name": "Project B"
    }
  ]
}
```

## Root Change Notifications

When the client's workspace changes, it notifies the server:

```json
{
  "jsonrpc": "2.0",
  "method": "notifications/roots/list_changed"
}
```

The server should re-fetch the roots list and adjust its behavior.

## Use Cases

- **File system servers** — Know which directories to index
- **Git servers** — Know which repositories to operate on
- **Search servers** — Scope searches to relevant locations
- **Linting servers** — Analyze code in the correct project context

## Best Practices

- Servers should **respect root boundaries** — don't access files outside roots
- Re-fetch roots when receiving change notifications
- Support multiple roots for multi-project workspaces
- Use roots as a security boundary to limit server access
