# MCP Glossary

Key terms used throughout the MCP documentation.

## A

**Annotations** — Metadata hints on tools (such as `readOnlyHint`, `destructiveHint`, and `idempotentHint`) describing behavior characteristics.

## C

**Capability** — A feature that a client or server declares support for during initialization (e.g., tools, resources, sampling).

**Client** — The MCP component within a host application that connects to a server. Each client has a 1:1 relationship with a server.

**Content Block** — A unit of data in tool results or prompts. Types include text, image, and resource.

**Cursor** — An opaque pagination token returned by a server (`nextCursor`) that the client sends in subsequent requests to fetch the next page of results.

## E

**Elicitation** — A server-initiated request for user input through the client, enabling interactive data collection.

## H

**Host** — The AI-powered application the user interacts with (e.g., Claude Desktop, Cursor). Manages one or more MCP clients.

## J

**JSON-RPC 2.0** — The wire protocol used by MCP for all messages. Defines requests, responses, and notifications.

## N

**Notification** — A one-way JSON-RPC message that doesn't expect a response.

## P

**Primitive** — One of the three core MCP abstractions: Tool, Resource, or Prompt.

**Progress Notification** — A server-to-client notification (`notifications/progress`) sent during a long-running operation to report ongoing progress.

**Progress Token** — An identifier provided by the client to track progress of long-running operations.

**Prompt** — A reusable template that structures LLM interactions. User-controlled.

## R

**Resource** — A data source identified by a URI. Application-controlled, read-only.

**Resource Template** — A parameterized URI pattern (RFC 6570) that allows servers to expose dynamic resources without listing each URI individually.

**Roots** — URIs provided by the client to the server indicating relevant workspace locations.

## S

**Sampling** — A server-initiated request for the client to perform an LLM completion.

**Session** — An active connection between a client and server with negotiated capabilities.

**Streamable HTTP** — The modern HTTP-based transport for remote MCP servers.

**Subscription** — A mechanism allowing clients to register interest in a resource URI (`resources/subscribe`) and receive real-time notifications when that resource changes.

## T

**Task** — An experimental abstraction for tracking long-running async operations.

**Tool** — An executable function exposed by a server. Model-controlled.

**Tool Annotations** — Metadata hints on tools (such as `readOnlyHint`, `destructiveHint`, and `idempotentHint`) describing behavior characteristics to help clients make execution decisions.

**Transport** — The communication mechanism between client and server (stdio, Streamable HTTP).
