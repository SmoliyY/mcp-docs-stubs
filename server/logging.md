# MCP Server Logging

MCP provides a built-in logging mechanism for servers to send diagnostic information to clients.

## Log Levels

MCP supports standard log levels:

| Level | Value | Usage |
|-------|-------|-------|
| `debug` | 0 | Detailed debugging information |
| `info` | 1 | General informational messages |
| `notice` | 2 | Normal but significant events |
| `warning` | 3 | Warning conditions |
| `error` | 4 | Error conditions |
| `critical` | 5 | Critical conditions |
| `alert` | 6 | Immediate action required |
| `emergency` | 7 | System is unusable |

## Sending Log Messages

Servers send logs via notifications:

```json
{
  "jsonrpc": "2.0",
  "method": "notifications/message",
  "params": {
    "level": "info",
    "logger": "database",
    "data": "Connected to PostgreSQL at localhost:5432"
  }
}
```

## Setting Log Level

Clients can set the minimum log level:

```json
{
  "jsonrpc": "2.0",
  "method": "logging/setLevel",
  "params": {
    "level": "warning"
  },
  "id": 1
}
```

After this, the server should only send logs at `warning` level or above.

## TypeScript Example

```typescript
server.sendLoggingMessage({
  level: "info",
  logger: "weather-api",
  data: "Fetching weather data for London"
});
```

## Python Example

```python
@mcp.tool()
async def fetch_data(ctx: Context, url: str) -> str:
    """Fetch data from a URL."""
    ctx.info(f"Fetching {url}")
    try:
        result = await http_get(url)
        ctx.debug(f"Response: {len(result)} bytes")
        return result
    except Exception as e:
        ctx.error(f"Failed to fetch {url}: {e}")
        raise
```

## Best Practices

- Use `debug` for verbose internal details
- Use `info` for normal operations
- Use `warning` for recoverable issues
- Use `error` for failures that affect functionality
- Include the `logger` field to identify the subsystem
