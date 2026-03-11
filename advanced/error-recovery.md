# Error Recovery in MCP

Robust error recovery ensures MCP integrations remain reliable even when things go wrong.

## Connection Recovery

### stdio Transport
If the server process crashes:

```typescript
class ResilientConnection {
  private retryCount = 0;
  private maxRetries = 3;

  async connect() {
    while (this.retryCount < this.maxRetries) {
      try {
        const transport = new StdioClientTransport(this.config);
        await this.client.connect(transport);
        this.retryCount = 0;
        return;
      } catch (error) {
        this.retryCount++;
        const delay = Math.min(1000 * Math.pow(2, this.retryCount), 30000);
        await sleep(delay);
      }
    }
    throw new Error("Max retries exceeded");
  }
}
```

### HTTP Transport
Handle network interruptions:

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
When a tool partially completes:

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

Prevent cascading failures by stopping calls to a failing server:

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
