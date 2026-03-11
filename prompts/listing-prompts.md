# Listing and Using Prompts

Clients discover and use prompts through standardized MCP methods.

## Listing Available Prompts

```json
{
  "jsonrpc": "2.0",
  "method": "prompts/list",
  "id": 1
}
```

Response:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "prompts": [
      {
        "name": "code_review",
        "description": "Review code for quality and bugs",
        "arguments": [
          {
            "name": "language",
            "description": "Programming language of the code",
            "required": true
          }
        ]
      },
      {
        "name": "explain_error",
        "description": "Explain an error message in simple terms",
        "arguments": [
          {
            "name": "error",
            "description": "The error message to explain",
            "required": true
          }
        ]
      }
    ]
  }
}
```

## Getting a Prompt

```json
{
  "jsonrpc": "2.0",
  "method": "prompts/get",
  "params": {
    "name": "explain_error",
    "arguments": {
      "error": "TypeError: Cannot read property 'map' of undefined"
    }
  },
  "id": 2
}
```

## Prompt List Changes

If the server supports `prompts: { listChanged: true }`:

```json
{
  "jsonrpc": "2.0",
  "method": "notifications/prompts/list_changed"
}
```

## How Clients Typically Present Prompts

- As **slash commands** (e.g., `/code_review`)
- In a **prompt picker** dropdown or menu
- As **quick actions** in the UI
- Through **keyboard shortcuts**

Prompts are user-initiated — unlike tools, the model does not decide to use them automatically.
