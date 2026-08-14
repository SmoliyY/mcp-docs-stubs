# Agent Patterns with MCP add

MCP provides the foundation for building sophisticated AI agent architectures. Here are common patterns.

## Tool-Using Agent

The simplest pattern: an LLM uses MCP tools to accomplish tasks.

```
User Request → LLM → Tool Call → MCP Server → Result → LLM → Response
```

The LLM decides which tools to call and orchestrates multi-step workflows.

## ReAct Pattern

Reasoning + Acting: The agent reasons about what to do, acts (calls a tool), observes the result, and repeats.

```
Think: I need to find the bug in the code
Act: search_files("error handling")
Observe: Found 3 files with error handling
Think: Let me read the main error handler
Act: read_file("/src/errorHandler.ts")
Observe: [file contents]
Think: The issue is in line 42...
```

## Multi-Agent with MCP

Multiple specialized agents, each with access to different MCP servers:

```
Orchestrator Agent
├── Code Agent → Filesystem Server, Git Server
├── Research Agent → Web Search Server, Browser Server
└── Communication Agent → Slack Server, Email Server
```

## Sampling-Enabled Agents

Servers use sampling to request LLM completions:

```
MCP Server receives tool call
  → Needs AI reasoning to process
  → Requests sampling from client
  → Client forwards to LLM
  → Server uses result to complete the tool call
```

## Memory-Augmented Agents

Use the Memory server to persist knowledge:

1. Agent encounters new information
2. Stores it via Memory server tools
3. On future tasks, queries Memory server
4. Uses retrieved context to inform decisions

## Guardrails

- Limit the number of tool calls per request
- Set timeouts for agent loops
- Require user confirmation for sensitive actions
- Log all agent decisions for auditing
- Implement circuit breakers for failing tools
