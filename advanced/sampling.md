# Sampling

Sampling allows MCP servers to **request LLM completions** through the client. This enables sophisticated agentic behaviors where the server can leverage the AI model's capabilities.

## How It Works

```
Server                     Client                    LLM
  │                          │                        │
  │ sampling/createMessage   │                        │
  ├─────────────────────────►│                        │
  │                          │  Forward to LLM        │
  │                          ├───────────────────────►│
  │                          │                        │
  │                          │  LLM Response          │
  │                          │◄───────────────────────┤
  │ Result                   │                        │
  │◄─────────────────────────┤                        │
```

## Prerequisites

The client must declare sampling support during initialization:

```json
{
  "capabilities": {
    "sampling": {}
  }
}
```

## Request

```json
{
  "jsonrpc": "2.0",
  "method": "sampling/createMessage",
  "params": {
    "messages": [
      {
        "role": "user",
        "content": {
          "type": "text",
          "text": "Summarize this document in 3 bullet points: ..."
        }
      }
    ],
    "maxTokens": 500,
    "temperature": 0.7,
    "systemPrompt": "You are a concise summarizer."
  },
  "id": 1
}
```

## Response

```json
{
  "role": "assistant",
  "content": {
    "type": "text",
    "text": "• Point 1...\n• Point 2...\n• Point 3..."
  },
  "model": "claude-sonnet-4-5-20250929",
  "stopReason": "endTurn"
}
```

## Human-in-the-Loop

Clients SHOULD show sampling requests to the user before forwarding to the LLM. The user can:
- Approve the request as-is
- Modify the messages
- Reject the request

## Use Cases

- **Data summarization** within a tool
- **Multi-step reasoning** for complex tasks
- **Content generation** as part of a workflow
- **Classification** of retrieved data
