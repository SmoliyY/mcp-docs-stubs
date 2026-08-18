# Elicitation

Elicitation allows Model Context Protocol (MCP) servers to request information from the user through the client.
This enables interactive data collection during tool execution, allowing servers to pause and wait for user input before continuing.

## Explanation

### What is elicitation?

Sometimes a tool needs additional input from the user to proceed that was not part of the initial request.
Elicitation provides a structured mechanism for servers to ask questions and receive answers.
Instead of failing when information is missing, a server can "elicit" the required data from the user.

### Key concepts

- **Interactive flow**: The server initiates the request, and the client manages the user interface for the response.
- **Structured data**: Servers can specify a JSON Schema to ensure the received data matches the expected format.
- **URL mode**: A specialized mode for collecting file paths or external links.
- **State management**: Elicitation is typically used within a tool execution where the server maintains state while waiting for the client.

### Use cases

- **Configuration**: Gathering setup parameters like database connection strings or API keys.
- **Confirmation**: Asking for explicit permission before performing destructive actions.
- **Data input**: Collecting credentials or connection strings.
- **Disambiguation**: Asking the user to choose between multiple possible interpretations of a request.
- **File collection**: Requesting a local file for processing via URL mode.
- **Link sharing**: Collecting references to external resources.

## How-to guides

### How to collect structured input

To collect structured data, send an `elicitation/create` request with a `requestedSchema`.
The client uses this schema to generate an appropriate input form for the user.

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

### How to request a file upload

Use URL mode when you need the user to provide a file or a link.
In this mode, the client typically provides a file picker or a text input for a URL.

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

### How to handle user responses

Servers must handle three types of responses from the client: `accept`, `decline`, and `cancel`.

1.  **Accept**: The user provided the data. Process the `content` field.
2.  **Decline**: The user explicitly refused to provide the data. The server should fail the operation gracefully.
3.  **Cancel**: The user closed the prompt without making a choice. The server should abort the current task.

### How to chain elicitation steps

You can perform multi-step elicitation by sending subsequent requests after receiving a response.
This is useful for building interactive wizards.

1.  Ask for a category (e.g., "Database").
2.  Wait for the response (`accept` with `database: "staging"`).
3.  Send a second request for a specific table based on the first answer.
4.  Wait for the second response.

## Technical reference

### Capabilities

Clients must declare support for elicitation in their initialization capabilities:

```json
{
  "capabilities": {
    "elicitation": {}
  }
}
```

### `elicitation/create` request

The server sends an `elicitation/create` request to the client.

| Field | Type | Description |
| :--- | :--- | :--- |
| `message` | `string` | The prompt shown to the user. |
| `requestedSchema` | `object` | (Optional) A JSON Schema defining the expected data. |
| `mode` | `string` | (Optional) Use `"url"` for file and link collection. Defaults to schema-based. |

### Response format

The client returns a response that indicates the user's action and any provided content.

```json
{
  "action": "accept" | "decline" | "cancel",
  "content": {
    "key": "value"
  }
}
```

### Response actions

| Action | Description |
| :--- | :--- |
| `accept` | The user provided the requested information. |
| `decline` | The user refused to provide the information. |
| `cancel` | The user canceled the operation. |

## Best practices

- Provide clear, specific questions.
- Use `requestedSchema` to constrain input for structured data.
- Use `mode: "url"` for file and link collection.
- Handle `decline` and `cancel` actions gracefully.
- Minimize the number of elicitation steps to avoid user fatigue.
