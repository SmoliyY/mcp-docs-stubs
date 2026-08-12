# Agent Patterns with MCP

MCP provides the foundation for building sophisticated AI agent architectures.
Here are common patterns.

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

## Implementation Example

The following TypeScript example demonstrates a basic agent loop using the `@modelcontextprotocol/sdk`.
This agent connects to a server, discovers tools, and implements a simple reasoning/acting cycle.

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio.js";

async function runAgent() {
  // 1. Initialize MCP client and transport
  const transport = new StdioClientTransport({
    command: "node",
    args: ["path/to/server.js"]
  });

  const client = new Client({
    name: "mcp-agent-example",
    version: "1.0.0"
  }, {
    capabilities: {}
  });

  await client.connect(transport);

  // 2. Discover available tools
  // The agent uses this list to know what capabilities are available
  const { tools } = await client.listTools();

  // 3. Simple Reasoning/Acting loop
  const task = "Search for 'error' in the project and describe the results.";
  let agentContext = [
    { role: "user", content: task }
  ];

  while (true) {
    // In a real implementation, you would pass 'agentContext' and 'tools'
    // to an LLM like Claude to decide the next step.
    const agentAction = await callLLM({
      messages: agentContext,
      tools: tools
    });

    if (agentAction.type === "text_response") {
      console.log("Agent finished:", agentAction.text);
      break;
    }

    if (agentAction.type === "tool_call") {
      console.log(`Agent calling tool: ${agentAction.toolName}`);

      // 4. Use MCP client to invoke the tool
      try {
        const toolResult = await client.callTool(
          agentAction.toolName,
          agentAction.arguments
        );

        // 5. Add observation back to context for next reasoning step
        agentContext.push({
          role: "assistant",
          content: `Called tool ${agentAction.toolName}`,
          toolCall: agentAction
        });
        agentContext.push({
          role: "tool_result",
          content: toolResult.content
        });
      } catch (error) {
        console.error("Tool execution failed:", error);
        break;
      }
    }
  }

  await client.close();
}

// Simulated LLM call
async function callLLM({ messages, tools }) {
  // Real implementation would use an AI SDK (like Anthropic's SDK)
  return {
    type: "tool_call",
    toolName: "search_files",
    arguments: { query: "error" }
  };
}
```

### Key SDK Primitives

- **`Client`**: The core class that manages the connection and communication with MCP servers.
- **`listTools()`**: Used during the discovery phase so the agent knows which tools are available.
- **`callTool(name, arguments)`**: The primary method for the "Act" phase in a ReAct loop, executing the chosen tool on the server.

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
