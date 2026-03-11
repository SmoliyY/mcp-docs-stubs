# The Context Object

In FastMCP (Python), the **Context** object provides tools with access to MCP server features during execution.

## Accessing Context

Add `ctx: Context` as the first parameter of your tool function:

```python
from mcp.server.fastmcp import FastMCP, Context

mcp = FastMCP("my-server")

@mcp.tool()
async def process_data(ctx: Context, input: str) -> str:
    """Process input data with context access."""
    ctx.info("Starting processing")
    result = await heavy_computation(input)
    ctx.info(f"Completed: {len(result)} bytes processed")
    return result
```

## Available Context Methods

### Logging

```python
ctx.debug("Detailed debug info")
ctx.info("Normal operation info")
ctx.warning("Something unexpected")
ctx.error("Something failed")
```

### Progress Reporting

```python
@mcp.tool()
async def import_records(ctx: Context, file: str) -> str:
    """Import records from a file."""
    records = load_records(file)
    for i, record in enumerate(records):
        await process(record)
        await ctx.report_progress(i + 1, len(records))
    return f"Imported {len(records)} records"
```

### Reading Resources

Tools can read resources from their own server:

```python
@mcp.tool()
async def analyze(ctx: Context) -> str:
    """Analyze configuration."""
    config = await ctx.read_resource("config://app")
    return f"Config has {len(config)} entries"
```

### Requesting Sampling

```python
@mcp.tool()
async def summarize(ctx: Context, text: str) -> str:
    """Summarize text using LLM sampling."""
    result = await ctx.sample(
        messages=[{"role": "user", "content": f"Summarize: {text}"}],
        max_tokens=200
    )
    return result.text
```

## TypeScript Equivalent

In TypeScript, context is available through the handler's second parameter:

```typescript
server.tool("process", "Process data", { input: z.string() },
  async ({ input }, { meta, sendProgress, log }) => {
    log("info", "Processing...");
    // meta contains progressToken, etc.
  }
);
```
