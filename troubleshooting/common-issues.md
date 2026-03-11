# Common MCP Issues and Solutions

This guide covers frequently encountered problems when working with MCP.

## Server Not Connecting

### Symptoms
- No tools appearing in the host
- Connection timeout errors

### Solutions
1. **Check JSON syntax** in config file — use a JSON validator
2. **Verify the command** exists and is executable
3. **Check file paths** — use absolute paths
4. **Restart the host** application after config changes
5. **Check server logs** — look at stderr output

## Tools Not Appearing

### Symptoms
- Server connects but no tools show up

### Solutions
1. Verify the server declares `tools` capability
2. Check that `tools/list` returns the expected tools
3. Test with the **MCP Inspector** to isolate the issue
4. Ensure tool names don't conflict with other servers

## Tool Calls Failing

### Symptoms
- Tools appear but return errors when called

### Solutions
1. **Check arguments** — ensure they match the input schema
2. **Check environment variables** — API keys, tokens, connection strings
3. **Test manually** — run the server standalone and test with Inspector
4. **Check permissions** — file access, network access, API scopes

## Connection Drops

### Symptoms
- Server disconnects unexpectedly

### Solutions
1. Check if the server **process is crashing** — review stderr
2. Look for **unhandled exceptions** in server code
3. Verify **memory usage** — servers shouldn't consume excessive RAM
4. Check for **timeout issues** — long operations may need progress reporting

## stdio Transport Issues

| Issue | Solution |
|-------|----------|
| Server writes to stdout | Only JSON-RPC messages on stdout |
| Newlines in messages | Ensure single-line JSON |
| Encoding issues | Use UTF-8 consistently |
| Server doesn't start | Check command and args in config |

## HTTP Transport Issues

| Issue | Solution |
|-------|----------|
| CORS errors | Configure CORS headers on server |
| Auth failures | Verify OAuth token and scopes |
| SSL errors | Use valid certificates |
| Session lost | Handle `Mcp-Session-Id` header |
