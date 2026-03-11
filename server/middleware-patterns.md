# Middleware Patterns for MCP Servers

Middleware patterns help add cross-cutting concerns like logging, validation, and rate limiting to MCP servers.

## Logging Middleware

Wrap tool handlers with automatic logging:

```typescript
function withLogging(handler: ToolHandler): ToolHandler {
  return async (args, context) => {
    const start = Date.now();
    console.error(`[${new Date().toISOString()}] Tool called with:`, args);

    try {
      const result = await handler(args, context);
      console.error(`[${new Date().toISOString()}] Completed in ${Date.now() - start}ms`);
      return result;
    } catch (error) {
      console.error(`[${new Date().toISOString()}] Failed:`, error.message);
      throw error;
    }
  };
}
```

## Rate Limiting

```typescript
const callCounts = new Map<string, number[]>();

function withRateLimit(maxCalls: number, windowMs: number) {
  return (handler: ToolHandler): ToolHandler => {
    return async (args, context) => {
      const now = Date.now();
      const key = context.toolName;
      const calls = callCounts.get(key) || [];

      // Remove old calls outside the window
      const recent = calls.filter(t => now - t < windowMs);

      if (recent.length >= maxCalls) {
        return {
          content: [{ type: "text", text: "Rate limit exceeded. Try again later." }],
          isError: true
        };
      }

      recent.push(now);
      callCounts.set(key, recent);
      return handler(args, context);
    };
  };
}
```

## Input Sanitization

```typescript
function withSanitization(handler: ToolHandler): ToolHandler {
  return async (args, context) => {
    const sanitized = {};
    for (const [key, value] of Object.entries(args)) {
      if (typeof value === "string") {
        sanitized[key] = value.replace(/[<>]/g, "");
      } else {
        sanitized[key] = value;
      }
    }
    return handler(sanitized, context);
  };
}
```

## Python Decorator Pattern

```python
from functools import wraps

def rate_limited(max_calls=10, window=60):
    calls = []
    def decorator(func):
        @wraps(func)
        async def wrapper(*args, **kwargs):
            now = time.time()
            calls[:] = [t for t in calls if now - t < window]
            if len(calls) >= max_calls:
                return "Rate limit exceeded"
            calls.append(now)
            return await func(*args, **kwargs)
        return wrapper
    return decorator
```
