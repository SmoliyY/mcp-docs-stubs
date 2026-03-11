# JSON-RPC 2.0 in MCP

MCP uses **JSON-RPC 2.0** as its wire protocol. Understanding JSON-RPC is fundamental to working with MCP at the protocol level.

## What is JSON-RPC?

JSON-RPC is a stateless, lightweight remote procedure call (RPC) protocol. It uses JSON as the data format and defines a simple request-response pattern.

## Specification

Every JSON-RPC message includes:

```json
{
  "jsonrpc": "2.0"
}
```

This version field is always `"2.0"` and is required.

## Message Types

### Request
Has `method`, `params` (optional), and `id`:

```json
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "params": { "name": "search", "arguments": {} },
  "id": 42
}
```

### Response
Has `result` or `error`, and matching `id`:

```json
{
  "jsonrpc": "2.0",
  "result": { "content": [...] },
  "id": 42
}
```

### Notification
Has `method` but NO `id`:

```json
{
  "jsonrpc": "2.0",
  "method": "notifications/initialized"
}
```

## MCP-Specific Methods

### Client → Server
| Method | Description |
|--------|-------------|
| `initialize` | Start connection |
| `tools/list` | List tools |
| `tools/call` | Call a tool |
| `resources/list` | List resources |
| `resources/read` | Read a resource |
| `prompts/list` | List prompts |
| `prompts/get` | Get a prompt |

### Server → Client
| Method | Description |
|--------|-------------|
| `sampling/createMessage` | Request LLM completion |
| `elicitation/create` | Request user input |
| `roots/list` | Request workspace roots |

## ID Management

- IDs can be strings or numbers
- IDs must be unique within a session
- Responses must include the matching request ID
- Notifications must NOT have an ID
