# Debugging MCP Servers

Effective debugging is essential for MCP server development. Here are tools and techniques to identify and fix issues.

## MCP Inspector

The fastest way to debug a server:

```bash
npx @modelcontextprotocol/inspector node path/to/server.js
```

The Inspector shows:
- Connection status
- Available tools/resources/prompts
- Request/response logs
- Error details

## Logging to stderr

In stdio transport, `stderr` is your debugging channel:

```typescript
// TypeScript
console.error("Debug: processing request for", toolName);
```

```python
# Python
import sys
print("Debug: processing request", file=sys.stderr)
```

## Claude Desktop Logs

Claude Desktop stores MCP logs at:

| OS | Path |
|----|------|
| macOS | `~/Library/Logs/Claude/mcp*.log` |
| Windows | `%APPDATA%\Claude\Logs\mcp*.log` |

## Enable Verbose Logging

### TypeScript
```typescript
const server = new McpServer({
  name: "my-server",
  version: "1.0.0"
});

// Send logs to the client
server.sendLoggingMessage({
  level: "debug",
  data: "Server initialized"
});
```

### Python
```python
import logging
logging.basicConfig(level=logging.DEBUG, stream=sys.stderr)
```

## Common Debugging Scenarios

### Tool Returns Wrong Data
1. Log the input arguments
2. Log the intermediate processing steps
3. Log the final result before returning
4. Compare with expected output

### Server Crashes on Startup
1. Run the server manually: `node server.js`
2. Check for missing dependencies
3. Verify environment variables
4. Check for syntax errors

### Initialization Fails
1. Check protocol version compatibility
2. Verify capability declarations
3. Look for errors in the initialize response
4. Test with a minimal client

## Testing Tools

```bash
# Send a raw JSON-RPC message to test
echo '{"jsonrpc":"2.0","method":"tools/list","id":1}' | node server.js
```
