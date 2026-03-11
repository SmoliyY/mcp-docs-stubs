# MCP Frequently Asked Questions

## General

### What does MCP stand for?
**Model Context Protocol** — an open standard for connecting AI models to external tools and data.

### Who created MCP?
MCP was created by **Anthropic** and released in November 2024. It is now governed by **The Linux Foundation**.

### Is MCP open source?
Yes. The specification, SDKs, and reference servers are all open source.

### Is MCP free to use?
Yes. MCP is free to implement, use, and build upon.

## Technical

### What protocol does MCP use?
**JSON-RPC 2.0** over various transports (stdio, Streamable HTTP).

### Can I use MCP with OpenAI models?
Yes. MCP is model-agnostic. Any LLM that supports function calling can work with MCP tools through a compatible host application.

### How is MCP different from function calling?
Function calling defines tools per API request. MCP provides persistent server connections with dynamic tool discovery, bidirectional communication, and a standardized ecosystem.

### Can MCP servers communicate with each other?
Not directly. Servers are isolated. The host application mediates all communication. The LLM can orchestrate workflows spanning multiple servers.

### What languages can I use to build MCP servers?
Any language, but official SDKs exist for **TypeScript**, **Python**, **Java/Kotlin**, and **C#**.

## Security

### Is MCP secure?
MCP includes security features (OAuth, scoping, user consent) but requires proper implementation. Follow security best practices and keep servers updated.

### Can MCP servers access my files?
Only if you configure them with file system access. Servers can only access what you explicitly allow.

### Should I trust third-party MCP servers?
Exercise caution. Review server code, check the source, and limit permissions. Only use servers from trusted sources.

## Getting Started

### What's the fastest way to try MCP?
Install Claude Desktop and add a filesystem server to the config file. See the Claude Desktop setup guide.

### Where can I find MCP servers?
The official GitHub organization hosts reference servers. Community registries and package managers (npm, PyPI) have many more.
