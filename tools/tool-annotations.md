# Tool Annotations

MCP tools can include **annotations** that provide metadata about the tool's behavior. Annotations help clients make informed decisions about tool execution.

## Available Annotations

| Annotation | Type | Description |
|-----------|------|-------------|
| `title` | string | Human-readable display name |
| `readOnlyHint` | boolean | Tool doesn't modify state |
| `destructiveHint` | boolean | Tool may perform destructive operations |
| `idempotentHint` | boolean | Repeated calls with same args have same effect |
| `openWorldHint` | boolean | Tool interacts with external entities |

## Example

```json
{
  "name": "delete_file",
  "description": "Delete a file from the filesystem",
  "annotations": {
    "title": "Delete File",
    "readOnlyHint": false,
    "destructiveHint": true,
    "idempotentHint": true,
    "openWorldHint": false
  },
  "inputSchema": {
    "type": "object",
    "properties": {
      "path": { "type": "string" }
    },
    "required": ["path"]
  }
}
```

## How Clients Use Annotations

- **destructiveHint: true** — Client may show a confirmation dialog
- **readOnlyHint: true** — Client may auto-approve without user confirmation
- **idempotentHint: true** — Client knows retrying is safe
- **openWorldHint: true** — Client knows the tool contacts external services

## Important Notes

- Annotations are **hints**, not guarantees
- Clients SHOULD respect annotations but MAY override them
- All annotation fields are optional
- Default values: `readOnlyHint: false`, `destructiveHint: true`, `idempotentHint: false`, `openWorldHint: true`
