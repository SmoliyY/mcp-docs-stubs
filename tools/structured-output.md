# Structured Tool Output

Starting with the **2025-06-18** specification, MCP tools support **structured output** alongside the traditional content array.

## Why Structured Output?

Previously, tool results were limited to content blocks (text, images). Structured output adds a machine-readable `structuredContent` field that follows a defined schema, making it easier for clients to programmatically process results.

## Defining Output Schema

Tools can declare an `outputSchema` in their definition:

```json
{
  "name": "get_user",
  "description": "Retrieve user information",
  "inputSchema": {
    "type": "object",
    "properties": {
      "userId": { "type": "string" }
    },
    "required": ["userId"]
  },
  "outputSchema": {
    "type": "object",
    "properties": {
      "name": { "type": "string" },
      "email": { "type": "string" },
      "role": { "type": "string" }
    },
    "required": ["name", "email"]
  }
}
```

## Returning Structured Content

When a tool has an `outputSchema`, the result includes `structuredContent`:

```json
{
  "structuredContent": {
    "name": "Alice Smith",
    "email": "alice@example.com",
    "role": "admin"
  }
}
```

## Rules

- If `outputSchema` is defined, the result **MUST** include `structuredContent`
- `structuredContent` must validate against the `outputSchema`
- The `content` array becomes optional when `structuredContent` is present
- Tools without `outputSchema` continue to use only the `content` array

## Benefits

- Type-safe tool results
- Client-side validation
- Better IDE integration and autocomplete
- Machine-readable results for agent pipelines
