# Key Concepts in MCP

Understanding MCP requires familiarity with several core concepts that form the foundation of the protocol.

## Hosts

A **Host** is the AI-powered application that the user interacts with. Examples include Claude Desktop, an IDE assistant, or a custom chatbot. The host manages one or more MCP clients.

## Clients

An **MCP Client** is a component within the host that maintains a 1:1 connection with an MCP server. The client handles protocol negotiation, capability exchange, and message routing.

## Servers

An **MCP Server** exposes capabilities to clients. Each server provides some combination of tools, resources, and prompts. Servers can be local processes or remote services.

## Primitives

MCP defines three core **primitives** that servers can expose:

1. **Tools** — Executable functions the LLM can invoke
2. **Resources** — Data sources providing contextual information
3. **Prompts** — Reusable instruction templates

## Capabilities

During initialization, both client and server exchange **capability declarations**. This tells each side what features the other supports (e.g., sampling, elicitation, roots).

## Sessions

A **session** represents an active connection between a client and server. Sessions maintain state including negotiated capabilities and active subscriptions.

## JSON-RPC 2.0

All MCP messages use the **JSON-RPC 2.0** format — a lightweight remote procedure call protocol encoded in JSON.

```json
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "params": { "name": "get_weather", "arguments": { "city": "London" } },
  "id": 1
}
```
