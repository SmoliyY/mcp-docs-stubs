# What is the Model Context Protocol (MCP)?

The **Model Context Protocol (MCP)** is an open standard introduced by Anthropic in November 2024. It standardizes how AI systems — such as large language models (LLMs) — integrate with external tools, data sources, and services.

## Why MCP Exists

Before MCP, every AI integration required custom glue code. Each tool, API, or database needed a bespoke connector. MCP solves this by providing a **universal interface** for:

- Reading files and data sources
- Executing functions and actions
- Handling contextual prompts
- Managing authentication and authorization

## Key Benefits

| Benefit | Description |
|---------|-------------|
| **Standardization** | One protocol for all integrations |
| **Interoperability** | Any MCP client works with any MCP server |
| **Security** | Built-in auth, scoping, and consent |
| **Extensibility** | Easy to add new capabilities |

## Who Uses MCP?

MCP has been adopted by major AI providers including **OpenAI**, **Google DeepMind**, and **Anthropic**. It is hosted by **The Linux Foundation** as an open-source project.

## Core Idea

Think of MCP as a **USB-C port for AI applications**. Just as USB-C provides a standard connection for devices, MCP provides a standard connection between AI models and the tools they need.

```
AI Application (Host)
    └── MCP Client
            └── MCP Server → External Tool / Data Source
```

MCP follows a client-server architecture using **JSON-RPC 2.0** as its messaging format.
