# Agent Patterns

Use the Model Context Protocol (MCP) to build different types of AI agents.
MCP provides a standard way for agents to interact with their environment.
These agents can be simple or complex.

## Agents and Tools

This pattern is the most simple.
An AI model uses MCP tools to do tasks.

```
User Request → AI Model → Tool Call → MCP Server → Result → AI Model → Response
```

The AI model selects tools.
It completes tasks that have many steps.

## ReAct Pattern

The Reasoning and Acting (ReAct) pattern is common.
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

## Implementation Example

Use the `@modelcontextprotocol/sdk` to build an agent loop in TypeScript.
This agent connects to a server.
It finds tools.
It starts a reasoning cycle.

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

  // 3. Simple reasoning and action loop
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

## Multi-Agent Systems

A multi-agent system divides a complex task between different agents.
Each agent can use its own MCP servers.

**Scenario example: Software development**
- **Orchestrator**: It receives the request from the user.
It selects an agent to do the task.
- **Coder agent**: It uses a **Filesystem Server** to write code.
It uses a **Git Server** to commit changes.
- **Reviewer agent**: It uses a **Linter Server** or **Test Runner** to verify code.
It reports bugs.

## Model Requests from Servers

A tool can ask the AI model for help during a task.
This process is **sampling**.

**Scenario example: Smart content summarizer**
1. The user calls the `summarize_resource` tool.
2. The server gets a large document from a database.
3. The server sends a `sampling/createMessage` request to the client.
4. The client asks the AI model to summarize the text.
5. The model sends the summary to the server.
6. The server sends the summary to the user.

## Agents with Memory

These agents use an MCP server to store information.
They can use this information in future tasks.

**Example: Personal preference tracking**
1. **Store**: The user says, "I prefer Python for backend projects."
The agent uses a **Memory Server** to save this information.
2. **Retrieve**: Later, the user says, "Start a new web project."
3. **Reason**: The agent searches the **Memory Server** for preferences.
4. **Act**: The agent finds the preference.
It says, "I will use Python because you prefer it."

## Guardrails

Use limits to keep agents safe:
- **Limit tool calls**: Stop the agent if it stays in a loop.
- **Set timeouts**: Do not let a task run for a long time.
- **Human-in-the-loop**: Ask the user for permission before a sensitive action.
For example, ask before the agent deletes a file.
- **Audit logs**: Record all decisions and tool calls.
- **Error handling**: Make sure the agent can continue if a tool fails.
