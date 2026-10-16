# Error Recovery in MCP

Robust error recovery ensures MCP integrations remain reliable even when things go wrong.

## Connection Recovery

### stdio Transport

When a server process crashes, the host application should attempt to reconnect using exponential backoff.
This prevents the host from entering a tight loop of failed process spawns.

```typescript
class ResilientConnection {
  private retryCount = 0;
  private maxRetries = 5;

  async connect(config: StdioServerParameters) {
    while (this.retryCount < this.maxRetries) {
      try {
        const transport = new StdioClientTransport(config);
        await this.client.connect(transport);
        
        // Reset retry count on successful connection
        this.retryCount = 0;

        // Monitor for unexpected exit after successful connection
        transport.onClose = () => {
          console.error("Server process exited unexpectedly");
          
          // Reconnecting will typically create a new transport.
          // Ensure the old transport is properly disposed of to reject pending requests.
          this.connect(config);
        };
        
        return;
      } catch (error) {
        this.retryCount++;
        if (this.retryCount >= this.maxRetries) {
          throw new Error(`Failed to connect after ${this.maxRetries} attempts: ${error}`);
        }

        const delay = Math.min(1000 * Math.pow(2, this.retryCount), 30000);
        console.warn(`Connection failed, retrying in ${delay}ms...`);
        await sleep(delay);
      }
    }
  }
}
```

### HTTP Transport

Handle network interruptions by monitoring the connection state and reconnecting when the stream closes.

```typescript
// Reconnect SSE stream on close
transport.onClose = async () => {
  console.error("Connection lost, reconnecting...");
  await sleep(1000);
  await transport.reconnect();
};
```

## Tool Call Recovery

### Retry Idempotent Tools

For tools that are safe to call multiple times (idempotent), implement a retry mechanism for transient errors.

```typescript
async function callToolWithRetry(client, name, args, maxRetries = 2) {
  for (let i = 0; i <= maxRetries; i++) {
    try {
      return await client.callTool(name, args);
    } catch (error) {
      if (i === maxRetries) throw error;
      await sleep(1000 * (i + 1));
    }
  }
}
```

### Handle Partial Failures

When a tool performs a batch operation, it should return detailed information about which items succeeded and which failed.

```typescript
server.tool("batch_process", "Process items", { items: z.array(z.string()) },
  async ({ items }) => {
    const results = [];
    const errors = [];

    for (const item of items) {
      try {
        results.push(await process(item));
      } catch (e) {
        errors.push(`${item}: ${e.message}`);
      }
    }

    return {
      content: [{
        type: "text",
        text: `Processed: ${results.length}/${items.length}\nErrors: ${errors.join("\n")}`
      }],
      isError: errors.length > 0
    };
  }
);
```

## Circuit Breaker Pattern

Prevent cascading failures by stopping calls to a failing server for a period of time.

```typescript
class CircuitBreaker {
  private failures = 0;
  private threshold = 5;
  private resetTime = 60000;
  private lastFailure = 0;

  canCall(): boolean {
    if (this.failures >= this.threshold) {
      return Date.now() - this.lastFailure > this.resetTime;
    }
    return true;
  }
}
```
