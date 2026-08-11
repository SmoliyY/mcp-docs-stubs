# Elicitation

Elicitation allows MCP servers to **request information from the user** through the client, enabling interactive data collection during tool execution.

## What Is Elicitation?

Sometimes a tool needs additional input from the user to proceed.
Elicitation provides a structured way for servers to ask questions and receive answers.

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

Servers send an `elicitation/create` request to the client.
By default, the request includes a `requestedSchema` defining the expected data.

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

The client returns a response indicating the user's action and any provided content.

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

## URL Mode

Servers can use **URL Mode** to request a URL from the user.
This mode is specifically designed for:
- **File uploads** — The client can handle local file selection and provide a URL to the server.
- **Link sharing** — The user can provide a reference to an external resource.

### URL Request

To request a URL, set the `mode` parameter to `"url"` and omit the `requestedSchema`:

```json
{
  "jsonrpc": "2.0",
  "method": "elicitation/create",
  "params": {
    "message": "Please upload the CSV file for analysis.",
    "mode": "url"
  },
  "id": 2
}
```

### URL Response

When the user provides a URL, the client returns the URL in the `content` object:

```json
{
  "action": "accept",
  "content": {
    "url": "https://example.com/uploads/data.csv"
  }
}
```

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
- **File uploads** — Requesting documents for processing
- **Link sharing** — Collecting references to external resources

## Best Practices

- Provide clear, specific questions
- Use `requestedSchema` to constrain input for structured data
- Use `mode: "url"` specifically for file and link collection
- Handle `decline` and `cancel` gracefully
- Minimize the number of elicitation steps
