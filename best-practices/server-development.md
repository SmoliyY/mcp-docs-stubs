# Server Development Best Practices

Follow these guidelines when building MCP servers to ensure reliability, security, and a great developer experience.

## Architecture Principles

### Single Responsibility
Each server should focus on one domain:
- A **filesystem** server handles files
- A **database** server handles queries
- A **GitHub** server handles repository operations

### Stateless Design
Where possible, design servers to be stateless. Store no session data between requests. This simplifies scaling and error recovery.

## Implementation Guidelines

### 1. Validate All Inputs
```typescript
server.tool("read_file", "Read a file", {
  path: z.string().refine(
    (p) => !p.includes(".."),
    "Path traversal not allowed"
  )
}, async ({ path }) => { ... });
```

### 2. Handle Errors Gracefully
Never let exceptions crash the server. Catch errors and return meaningful messages.

### 3. Use Appropriate Annotations
Mark tools as read-only, destructive, or idempotent to help clients make decisions.

### 4. Implement Pagination
If you have many tools, resources, or prompts, implement pagination to avoid overwhelming clients.

### 5. Support Logging
Use MCP's built-in logging to send diagnostic info to clients.

## Performance Tips

- Cache expensive computations
- Use connection pooling for databases
- Stream large responses when possible
- Set appropriate timeouts for external API calls

## Testing

- Use the **MCP Inspector** during development
- Write unit tests for tool logic
- Test with multiple clients (Claude Desktop, Cursor, etc.)
- Test error scenarios and edge cases

## Documentation

- Write clear tool descriptions
- Document required environment variables
- Provide a README with setup instructions
- Include example configurations for popular hosts
