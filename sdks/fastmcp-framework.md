# FastMCP Framework

**FastMCP** is a high-level Python framework included in the official `mcp` package. It dramatically simplifies MCP server development.

## Philosophy

FastMCP follows the principle of **convention over configuration**. Type hints and decorators replace boilerplate, and schemas are auto-generated.

## Core Features

### Decorator-Based Registration

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("my-server")

@mcp.tool()
def calculate(expression: str) -> str:
    """Evaluate a mathematical expression."""
    return str(eval(expression))  # Simplified example

@mcp.resource("status://health")
def health_check() -> str:
    """Server health status."""
    return "OK"

@mcp.prompt()
def analyze(topic: str) -> str:
    """Generate an analysis prompt."""
    return f"Please analyze the following topic: {topic}"
```

### Automatic Schema Generation

FastMCP inspects function signatures and generates JSON Schemas:

```python
@mcp.tool()
def search(
    query: str,
    max_results: int = 10,
    include_archived: bool = False
) -> str:
    """Search for documents."""
    ...
```

This generates:
```json
{
  "name": "search",
  "inputSchema": {
    "type": "object",
    "properties": {
      "query": { "type": "string" },
      "max_results": { "type": "integer", "default": 10 },
      "include_archived": { "type": "boolean", "default": false }
    },
    "required": ["query"]
  }
}
```

### Context Access

```python
from mcp.server.fastmcp import Context

@mcp.tool()
async def process(ctx: Context, data: str) -> str:
    """Process data with logging."""
    ctx.info("Starting processing")
    result = await heavy_computation(data)
    ctx.info("Processing complete")
    return result
```

### Image Support

```python
from mcp.server.fastmcp import Image

@mcp.tool()
def create_chart(data: str) -> Image:
    """Generate a chart from data."""
    chart_bytes = generate_chart(data)
    return Image(data=chart_bytes, format="png")
```

## Running

```bash
mcp run server.py                          # stdio
mcp run server.py --transport streamable-http  # HTTP
mcp dev server.py                          # Inspector
```
