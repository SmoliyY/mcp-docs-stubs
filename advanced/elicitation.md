# Elicitation

Elicitation allows MCP servers to **request information from the user** through the client, enabling interactive data collection during tool execution.

## What Is Elicitation?

Sometimes a tool needs additional input from the user to proceed. Elicitation provides a structured way for servers to ask questions and receive answers.

## Prerequisites

The client must declare elicitation support:

```json
{
  "capabilities": {
    "elicitation": {}
  }
}
```

## Request

```json
{
  "jsonrpc": "2.0",
  "method": "elicitation/create",
  "params": {
    "message": "Which database would you like to query?",
    "requestedSchema": {
      "type": "object",
      "properties": {
        "database": {
          "type": "string",
          "enum": ["production", "staging", "development"],
          "description": "Target database"
        }
      },
      "required": ["database"]
    }
  },
  "id": 1
}
```

## Response

```json
{
  "action": "accept",
  "content": {
    "database": "staging"
  }
}
```

## Response Actions

| Action | Description |
|--------|-------------|
| `accept` | User provided the requested information |
| `decline` | User refused to provide the information |
| `cancel` | User cancelled the operation |

## Multi-Step Elicitation

Servers can chain elicitation steps, each building on the previous:

1. Ask which database → User picks "staging"
2. Ask which table → User picks "users"
3. Ask for query parameters → User provides filters

## Use Cases

- **Configuration wizards** — Gathering setup parameters
- **Confirmation dialogs** — "Are you sure you want to delete?"
- **Data input** — Collecting credentials or connection strings
- **Disambiguation** — Clarifying ambiguous requests

## Best Practices

- Provide clear, specific questions
- Use `requestedSchema` to constrain input
- Handle `decline` and `cancel` gracefully
- Minimize the number of elicitation steps
