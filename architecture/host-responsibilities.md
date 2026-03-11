# Host Application Responsibilities

The **host** is the AI-powered application that users interact with. It has critical responsibilities in the MCP architecture.

## Core Responsibilities

### 1. Server Lifecycle Management
The host manages MCP server processes:
- **Spawn** servers based on configuration
- **Monitor** server health and responsiveness
- **Restart** crashed servers when appropriate
- **Shutdown** servers cleanly when the host closes

### 2. Client Management
The host creates and manages MCP clients:
- One client per server connection
- Handle initialization and capability exchange
- Route messages between LLM and appropriate servers

### 3. User Consent and Approval
The host MUST mediate between servers and users:
- **Display tool calls** before execution
- **Require approval** for destructive operations
- **Show sampling requests** from servers
- **Present elicitation dialogs** from servers

### 4. Security Enforcement
The host is the security boundary:
- Validate server identity
- Enforce permission policies
- Monitor for suspicious behavior
- Protect user data from unauthorized access

### 5. Tool Presentation
The host presents MCP tools to the LLM:
- Aggregate tools from all connected servers
- Format tool schemas for the LLM's function calling interface
- Handle tool name conflicts between servers
- Filter tools based on context or user preferences

### 6. Context Management
The host manages what information reaches the LLM:
- Include relevant resource content in prompts
- Manage context window usage
- Prioritize information based on relevance

## Implementation Checklist

- [ ] Server configuration and discovery
- [ ] Process spawning and management
- [ ] MCP client initialization
- [ ] Tool aggregation and deduplication
- [ ] User approval workflows
- [ ] Error handling and recovery
- [ ] Logging and diagnostics
