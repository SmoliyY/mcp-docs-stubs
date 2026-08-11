# Elicitation

Elicitation allows MCP servers to request information from the user through the client.
This enables interactive data collection during tool execution.

## What is elicitation?

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
By default, the request includes a `requestedSchema` that defines the expected data.

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

The client returns a response that indicates the user's action and any provided content.

```json
{
  "action": "accept",
  "content": {
    "database": "staging"
  }
}
```

## Response actions

| Action | Description |
| :--- | :--- |
| `accept` | The user provided the requested information |
| `decline` | The user refused to provide the information |
| `cancel` | The user canceled the operation |

## URL mode

Use URL mode when you need the user to provide a URL.
Common scenarios include:

- **File uploads**: The client lets the user pick a local file and returns a URL.
- **Link sharing**: The user provides a URL to an external resource.

### URL request

To request a URL, set `mode` to `"url"` and do not include `requestedSchema`.

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

### URL response

When the user provides a URL, the client returns it in the `content` object.

```json
{
  "action": "accept",
  "content": {
    "url": "https://example.com/uploads/data.csv"
  }
}
```

## Multi-step elicitation

You can chain elicitation steps, where each step builds on the previous one:

1. Ask for the database. The user picks "staging".
2. Ask for the table. The user picks "users".
3. Ask for query parameters. The user provides filters.

## Use cases

- **Configuration wizards**: Gather setup parameters
- **Confirmation dialogs**: Ask "Are you sure you want to delete?"
- **Data input**: Collect credentials or connection strings
- **Disambiguation**: Clarify ambiguous requests
- **File uploads**: Request documents for processing
- **Link sharing**: Collect references to external resources

## Best practices

- Provide clear, specific questions.
- Use `requestedSchema` to constrain input for structured data.
- Use `mode: "url"` for file and link collection.
- Handle `decline` and `cancel` actions gracefully.
- Minimize the number of elicitation steps.
