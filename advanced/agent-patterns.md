# Agent patterns

Use the Model Context Protocol (MCP) to build different types of AI agents.
MCP provides a standard way for agents to interact with their environment.
These agents can be simple or complex.

## Agents and tools

This pattern is the simplest.
An AI model uses MCP tools to do tasks.

```
User Request → AI Model → Tool Call → MCP Server → Result → AI Model → Response
```

The AI model selects tools.
It completes tasks with many steps.

## ReAct pattern

The ReAct (Reason and Act) pattern is common.
The agent thinks about an action.
It does the action.
Then, it observes the result.

```
Think: I need to find the bug in the code.
Act: call search_files("error handling")
Observe: Found 3 files with error handling.
Think: Let me read the main error handler.
Act: call read_file("/src/errorHandler.ts")
Observe: [file contents]
Think: The issue is in line 42...
```

The agent repeats this cycle until the task is complete.

## Implementation example

Use the `@modelcontextprotocol/sdk` to build an agent loop in TypeScript.
This agent connects to a server.
It finds tools.
It starts a loop to reason and act.

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

  // 2. Find available tools
  // The agent uses this list to find allowed actions
  const { tools } = await client.listTools();

  // 3. Simple loop to reason and act
  const task = "Search for 'error' in the project and describe the results.";
  let agentContext = [
    { role: "user", content: task }
  ];

  const maxSteps = 8;
  let steps = 0;
  let reachedMaxSteps = true;

  while (steps < maxSteps) {
    steps += 1;
    // Pass 'agentContext' and 'tools' to an AI model to decide the next step.
    const agentAction = await callLLM({
      messages: agentContext,
      tools: tools
    });

    if (agentAction.type === "text_response") {
      console.log("Agent finished:", agentAction.text);
      reachedMaxSteps = false;
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

        // 5. Add the result to the context for the next step
        agentContext.push({
          role: "assistant",
          content: `Called tool ${agentAction.toolName}`,
          toolCall: agentAction
        });
        agentContext.push({
          role: "tool",
          content: toolResult.content
        });
      } catch (error) {
        console.error("Tool execution failed:", error);
        break;
      }
    }
  }

  if (reachedMaxSteps) {
    console.warn("Agent stopped after reaching max steps.");
  }

  await client.close();
}

// Simplified AI model call
async function callLLM({ messages, tools }) {
  // An implementation uses an AI provider SDK
  const lastMessage = messages[messages.length - 1];
  const hasToolResult = lastMessage?.role === "tool";

  if (hasToolResult) {
    return {
      type: "text_response",
      text: "I found matches for 'error' in the project and summarized them."
    };
  }

  return {
    type: "tool_call",
    toolName: "search_files",
    arguments: { query: "error" }
  };
}
```

## Multi-agent systems

A multi-agent system divides a complex task between different agents.
Each agent can use its own MCP servers.

### Scenario example: Software development

- **Orchestrator**: This agent receives the request from the user and selects an agent to do the task.
- **Coder agent**: This agent uses a **Filesystem Server** to write code and a **Git Server** to commit changes.
- **Reviewer agent**: This agent uses a **Linter Server** or **Test Runner** to verify code and reports bugs.

## Model requests from servers

A tool can ask the AI model for help during a task.
This process is **sampling**.

### Scenario example: Smart content summarizer

1. The user calls the `summarize_resource` tool.
2. The server gets a large document from a database.
3. The server sends a `sampling/createMessage` request to the client.
4. The client asks the AI model to summarize the text.
5. The model sends the summary to the server.
6. The server sends the summary to the user.

## Agents with memory

These agents use an MCP server to store information.
They can use this information in future tasks.

### Example: Personal preferences

1. **Store**: The user says, "I prefer Python for backend projects."
2. **Save**: The agent uses a **Memory Server** to save this information.
3. **Retrieve**: Later, the user says, "Start a new web project."
4. **Search**: The agent searches the **Memory Server** for preferences.
5. **Act**: The agent finds the preference and uses it to complete the task.

## Guardrails

Use limits to keep agents safe:
- **Limit tool calls**: Stop the agent if it stays in a loop.
- **Set timeouts**: Do not let a task run for a long time.
- **Human-in-the-loop**: Ask the user for permission before a sensitive action.
- **Audit logs**: Record all decisions and tool calls.
- **Error handling**: Make sure the agent can continue if a tool fails.
