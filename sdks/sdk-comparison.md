# SDK Comparison

This guide compares the official MCP SDKs to help you choose the right one for your project.

## Feature Matrix

| Feature | TypeScript | Python | Java | C# |
|---------|-----------|--------|------|----|
| Server support | Yes | Yes | Yes | Yes |
| Client support | Yes | Yes | Yes | Yes |
| High-level API | McpServer | FastMCP | Spring Boot | ASP.NET Core |
| Low-level API | Server | Server | McpServer | McpServer |
| stdio transport | Yes | Yes | Yes | Yes |
| Streamable HTTP | Yes | Yes | Yes | Yes |
| Schema library | Zod | Type hints | Manual | Attributes |
| OAuth support | Yes | Yes | Partial | Partial |

## Choosing an SDK

### TypeScript
**Best for:** Web developers, Node.js backends, Cloudflare Workers

Pros:
- Largest ecosystem of examples
- Zod for type-safe schemas
- Excellent for serverless deployment

### Python
**Best for:** Data science, rapid prototyping, scripting

Pros:
- FastMCP makes development very fast
- Type hints auto-generate schemas
- Great for wrapping Python libraries

### Java/Kotlin
**Best for:** Enterprise applications, Spring Boot ecosystems

Pros:
- Spring Boot integration
- Strong typing
- Enterprise-grade error handling

### C#
**Best for:** .NET applications, Azure deployments

Pros:
- ASP.NET Core integration
- Attribute-based tool definition
- Windows-native support

## Schema Definition Comparison

### TypeScript (Zod)
```typescript
{ query: z.string().describe("Search query") }
```

### Python (Type Hints)
```python
def search(query: str) -> str:
    """Search query parameter comes from the type hint."""
```

### Java (Manual)
```java
new ToolSchema().property("query", "string", "Search query")
```

### C# (Attributes)
```csharp
[McpParameter("query", Required = true)]
public string Query { get; set; }
```
