# Using the MCP Inspector

The **MCP Inspector** is an interactive debugging tool for testing MCP servers during development.

## What Is It? push

The Inspector provides a web-based UI that connects to your MCP server, letting you:
- View available tools, resources, and prompts
- Execute tools with custom arguments
- Read resources
- Test prompts
- View raw JSON-RPC messages

## Running the Inspector

### With npx
```bash
npx @modelcontextprotocol/inspector node path/to/server.js
```

### With Python servers
```bash
npx @modelcontextprotocol/inspector uv run python server.py
```

### With the `mcp` CLI
```bash
mcp dev server.py
```

## Inspector Features

### Tools Tab
- Lists all tools with their descriptions and schemas
- Form-based input for tool parameters
- Displays results inline
- Shows raw request/response JSON

### Resources Tab
- Lists static resources and templates
- One-click resource reading
- Template parameter input
- Content preview with syntax highlighting

### Prompts Tab
- Lists available prompts
- Argument input forms
- Preview generated messages

### Messages Tab
- Real-time log of all JSON-RPC messages
- Filter by message type
- Copy individual messages

## Environment Variables

Pass environment variables to the server:

```bash
npx @modelcontextprotocol/inspector \
  -e API_KEY=sk-123 \
  -e DB_URL=postgres://localhost/mydb \
  node server.js
```

## Tips

- Use the Inspector during development to validate your server
- Check that tool descriptions make sense to an LLM
- Verify input schemas match expected formats
- Test edge cases and error handling
