# Agent Patterns

You can use the Model Context Protocol (MCP) to build many different types of AI agents.
From simple tool-users to complex multi-agent systems, MCP provides a standard way for agents to interact with their environment.

## Tool-Using Agent

This is the simplest pattern.
An AI model uses MCP tools to perform tasks it can't do on its own.

```
User Request → AI Model → Tool Call → MCP Server → Result → AI Model → Response
```

The AI model picks which tools to use and handles tasks that take several steps.

## ReAct Pattern

"Reasoning and Acting" (ReAct) is a common pattern where an agent thinks about what to do, takes an action, and then observes the result.

```
Think: I need to find the bug in the code.
Act: call search_files("error handling")
Observe: Found 3 files with error handling.
Think: Let me read the main error handler.
Act: call read_file("/src/errorHandler.ts")
Observe: [file contents]
Think: The issue is in line 42...
```

The agent repeats this cycle until it completes the task.

## Implementation Example

Here is how you can build a basic agent loop in TypeScript using the `@modelcontextprotocol/sdk`.
This agent connects to a server, finds available tools, and runs a simple reasoning cycle.

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio.js";

async function runAgent() {
  // 1. Set up the connection to an MCP server
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
  // The agent uses this list to know what it is allowed to do
  const { tools } = await client.listTools();

  // 3. Simple Reasoning/Acting loop
  const task = "Search for 'error' in the project and describe the results.";
  let agentContext = [
    { role: "user", content: task }
  ];

  while (true) {
    // In a real app, you would pass 'agentContext' and 'tools'
    // to an AI model like Claude to decide the next step.
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

      // 4. Run the tool on the MCP server
      try {
        const toolResult = await client.callTool(
          agentAction.toolName,
          agentAction.arguments
        );

        // 5. Add the result back to the context for the next step
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

// Simplified AI model call
async function callLLM({ messages, tools }) {
  // A real implementation would use an AI provider's SDK
  return {
    type: "tool_call",
    toolName: "search_files",
    arguments: { query: "error" }
  };
}
```

### Key SDK Primitives

- **`Client`**: The main class that connects to and talks with MCP servers.
- **`listTools()`**: Finds available tools so the agent knows what it can do.
- **`callTool(name, arguments)`**: Runs a tool on the server and gets the result back.

## Multi-Agent Systems

In a multi-agent system, you divide a complex task among several specialized agents.
Each agent can have its own set of MCP servers.

**Example Scenario: Software Development**
- **Orchestrator**: Receives the user's request and decides which specialized agent should handle it.
- **Coder Agent**: Uses a **Filesystem Server** to write code and a **Git Server** to commit changes.
- **Reviewer Agent**: Uses a **Linter Server** or **Test Runner** to verify the code and report bugs.

```
Orchestrator Agent
├── Coder Agent → Filesystem Server, Git Server
└── Reviewer Agent → Linter Server, Test Runner
```

## Sampling-Enabled Agents

Sometimes a tool needs to ask the AI model for help during its execution.
This is called **sampling**.

**Example Scenario: Smart Content Summarizer**
1. The user calls a `summarize_resource` tool on an MCP server.
2. The server fetches a very large document from a database.
3. To summarize it, the server sends a `sampling/createMessage` request back to the client.
4. The client asks the AI model to summarize the text.
5. The model provides the summary to the server.
6. The server returns the final summary to the user.

This allows tools to use the AI's reasoning powers directly.

## Memory-Augmented Agents

These agents use a specialized MCP server to remember information across different conversations or tasks.

**Example Sequence: Personal Preference Tracking**
1. **Store**: The user says, "I prefer using Python for backend projects." The agent calls a tool on a **Memory Server** to save this fact.
2. **Retrieve**: Days later, the user says, "Start a new web project."
3. **Reason**: The agent first searches its **Memory Server** for "frontend" or "backend" preferences.
4. **Act**: The agent finds the Python preference and says, "I'll set that up with Python since you mentioned you prefer it."

## Guardrails

When building agents, it is important to set limits to keep them safe and predictable:

- **Limit tool calls**: Stop the agent if it gets stuck in an infinite loop.
- **Set timeouts**: Don't let a single task run forever.
- **Human-in-the-loop**: Ask the user for permission before performing sensitive actions (like deleting files or sending emails).
- **Audit logs**: Record every decision and tool call the agent makes.
- **Error handling**: Make sure the agent can recover gracefully if a tool fails.
