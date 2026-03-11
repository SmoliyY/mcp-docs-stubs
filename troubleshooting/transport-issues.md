# Transport-Specific Troubleshooting

Different transports have different failure modes. This guide covers transport-specific issues.

## stdio Issues

### Server Writes Non-JSON to stdout
**Symptom:** Client receives parse errors.
**Cause:** `console.log()` or `print()` writing to stdout.
**Fix:** Use `console.error()` / `print(..., file=sys.stderr)` for debugging.

```python
# Wrong
print("Debug info")  # Goes to stdout, breaks protocol

# Right
print("Debug info", file=sys.stderr)
```

### Process Exits Immediately
**Symptom:** Connection closes right after opening.
**Cause:** Missing event loop, unhandled exception, or missing dependencies.
**Fix:** Run the server manually and check stderr:
```bash
node server.js 2>stderr.log
echo '{"jsonrpc":"2.0","method":"initialize","params":{},"id":1}' | node server.js
```

### Encoding Issues
**Symptom:** Garbled text or invalid JSON errors.
**Fix:** Ensure UTF-8 encoding:
```typescript
process.stdin.setEncoding("utf-8");
process.stdout.setDefaultEncoding("utf-8");
```

## Streamable HTTP Issues

### CORS Errors
**Symptom:** Browser-based clients can't connect.
**Fix:** Add CORS headers:
```typescript
app.use((req, res, next) => {
  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "Content-Type, Authorization, Mcp-Session-Id");
  next();
});
```

### Session Loss
**Symptom:** Server doesn't recognize returning clients.
**Fix:** Ensure the client sends the `Mcp-Session-Id` header on every request.

### SSE Stream Closes
**Symptom:** Server-initiated notifications stop arriving.
**Fix:** Implement reconnection logic with exponential backoff.

### Proxy Issues
**Symptom:** Requests timeout or fail through reverse proxies.
**Fix:** Configure proxy to support SSE:
```nginx
proxy_buffering off;
proxy_cache off;
proxy_read_timeout 86400s;
```

## General Transport Tips

- Always implement connection health checks
- Log all transport-level errors
- Handle graceful shutdown on both sides
- Test with network interruption simulation
