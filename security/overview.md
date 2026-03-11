# MCP Security Overview

Security is a critical concern in MCP because servers can access sensitive data and perform privileged operations. The protocol includes several mechanisms to ensure safe operation.

## Threat Model

MCP faces several security challenges:

| Threat | Description |
|--------|-------------|
| **Prompt Injection** | Malicious data in tool results manipulating the LLM |
| **Data Exfiltration** | Combining tools to leak sensitive information |
| **Tool Squatting** | Malicious servers mimicking trusted tool names |
| **Unauthorized Access** | Servers accessing data beyond their scope |
| **Man-in-the-Middle** | Intercepting HTTP transport communications |

## Security Principles

### Principle of Least Privilege
Servers should request only the permissions they need. Clients should grant only what's necessary.

### User Consent
Hosts MUST obtain user approval before:
- Connecting to new MCP servers
- Executing tools (especially destructive ones)
- Sharing data with servers

### Data Validation
- Clients should validate tool results before passing to the LLM
- Servers should validate all input parameters
- Neither side should trust the other implicitly

### Transport Security
- Use TLS for HTTP-based transports
- stdio transport is secure by default (local only)
- Validate server identity for remote connections

## Security Checklist

- [ ] Validate all inputs on the server side
- [ ] Implement proper authentication for remote servers
- [ ] Use scoped permissions (don't grant blanket access)
- [ ] Log all tool invocations for auditing
- [ ] Show tool calls to users for transparency
- [ ] Sanitize data before including in LLM context
