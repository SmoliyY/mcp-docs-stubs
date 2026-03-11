# Capability Negotiation

During initialization, both the MCP client and server declare their **capabilities**. This handshake determines what features are available during the session.

## Client Capabilities

Clients can declare support for:

| Capability | Description |
|-----------|-------------|
| `sampling` | Client supports LLM sampling requests from the server |
| `roots` | Client can provide filesystem root URIs |
| `elicitation` | Client supports interactive prompting of the user |

### Example Client Capabilities

```json
{
  "capabilities": {
    "sampling": {},
    "roots": {
      "listChanged": true
    },
    "elicitation": {}
  }
}
```

## Server Capabilities

Servers can declare support for:

| Capability | Description |
|-----------|-------------|
| `tools` | Server exposes callable tools |
| `resources` | Server provides readable resources |
| `prompts` | Server offers prompt templates |
| `logging` | Server supports log messages |

### Example Server Capabilities

```json
{
  "capabilities": {
    "tools": {
      "listChanged": true
    },
    "resources": {
      "subscribe": true,
      "listChanged": true
    },
    "prompts": {
      "listChanged": true
    },
    "logging": {}
  }
}
```

## `listChanged` Flag

When `listChanged` is `true`, the server will send notifications when its list of tools, resources, or prompts changes. The client should re-fetch the list when notified.

## Behavior Rules

- Clients MUST NOT call methods for capabilities the server didn't declare
- Servers MUST NOT send sampling requests if the client didn't declare sampling support
- Unknown capabilities should be ignored, not rejected
