# C# SDK

The official C# SDK enables building MCP servers and clients in .NET applications.

## Installation

```bash
dotnet add package ModelContextProtocol
```

## Quick Start — Server

```csharp
using ModelContextProtocol;
using ModelContextProtocol.Server;

var builder = McpServerBuilder.Create(args);

builder.AddTool("greet", "Greet a user by name",
    new { name = new { type = "string", description = "User's name" } },
    async (arguments) =>
    {
        var name = arguments["name"]?.ToString();
        return McpToolResult.Text($"Hello, {name}!");
    });

var server = builder.Build();
await server.RunAsync();
```

## ASP.NET Core Integration

```csharp
var builder = WebApplication.CreateBuilder(args);
builder.Services.AddMcp(options =>
{
    options.ServerName = "my-mcp-server";
    options.ServerVersion = "1.0.0";
});

var app = builder.Build();
app.MapMcp("/mcp");
app.Run();
```

## Defining Tools with Attributes

```csharp
[McpTool("calculate", "Perform a calculation")]
public class CalculateTool
{
    [McpParameter("expression", Required = true)]
    public string Expression { get; set; }

    public async Task<McpToolResult> ExecuteAsync()
    {
        var result = Evaluate(Expression);
        return McpToolResult.Text(result.ToString());
    }
}
```

## Transport Support

- **stdio** — `StdioTransport`
- **Streamable HTTP** — Via ASP.NET Core middleware

## Repository

`github.com/modelcontextprotocol/csharp-sdk`
