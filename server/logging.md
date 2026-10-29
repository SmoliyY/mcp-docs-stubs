# MCP Server Logging

MCP supports two distinct channels for logging: **Protocol Logging** and **Transport Logging**.
Understanding when to use each is crucial for both user experience and debugging.

## Protocol and Transport Logging

| Feature | Protocol Logging | Transport Logging |
| :--- | :--- | :--- |
| **Mechanism** | `notifications/message` | `stderr` |
| **Audience** | End-users / Client UI | Developers / System Administrators |
| **Visibility** | Surfaces in the host application's UI | Found in host logs or terminal output |
| **Use Case** | Status updates for the user | Low-level debugging and diagnostics |

### Protocol Logging

Protocol logging uses the MCP `notifications/message` method.
These logs are structured JSON-RPC messages sent over the established transport.

Because these messages are part of the protocol, host applications may display them directly to the user.
Use this channel for:
- Informing the user about long-running tasks.
- Providing status updates on tool execution.
- Surfacing recoverable warnings that a user might need to act upon.

See [Send Log Messages](#send-log-messages) for implementation details.

### Transport Logging

Transport logging involves writing directly to the error stream (typically `stderr`).
This is an out-of-band communication channel that bypasses the MCP protocol.

This channel is primarily for developers.
Hosts should capture this output and store it in log files for troubleshooting.
Use this channel for:
- Internal debugging information that would clutter the user UI.
- Stack traces and crash reports.
- Initialization logs before the MCP connection is fully established.

> [!WARNING]
> When using the **stdio transport**, you MUST NOT write log messages to `stdout`.
> Since `stdout` is reserved for the JSON-RPC protocol stream, any non-protocol data (like plain text logs) will corrupt the stream and cause the connection to fail.
> Always use `stderr` for out-of-band logging.

For more on debugging with `stderr`, see [Debugging MCP Servers](../troubleshooting/debugging-servers.md) and the [stdio transport reference](../transports/stdio.md).

## Log Levels

MCP supports standard log levels:

| Level | Value | Usage |
| :--- | :--- | :--- |
| `debug` | 0 | Detailed debugging information |
| `info` | 1 | General informational messages |
| `notice` | 2 | Normal but significant events |
| `warning` | 3 | Warning conditions |
| `error` | 4 | Error conditions |
| `critical` | 5 | Critical conditions |
| `alert` | 6 | Immediate action required |
| `emergency` | 7 | System is unusable |

## Send Log Messages

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

## Set Log Level

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
