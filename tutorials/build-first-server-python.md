# Build Your First MCP Server (Python)

Create a simple MCP server in Python using FastMCP with minimal boilerplate.

## Prerequisites

- Python 3.10+
- `uv` or `pip`

## Step 1: Set Up the Project

```bash
mkdir my-mcp-server && cd my-mcp-server
uv init
uv add mcp
```

## Step 2: Create the Server

Create `server.py`:

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("weather-server")

@mcp.tool()
def get_weather(city: str, units: str = "celsius") -> str:
    """Get the current weather for a city.

    Args:
        city: The name of the city
        units: Temperature units (celsius or fahrenheit)
    """
    # In a real server, call a weather API
    symbol = "C" if units == "celsius" else "F"
    return f"Weather in {city}: 22°{symbol}, sunny"

@mcp.resource("weather://cities")
def list_cities() -> str:
    """List available cities for weather lookup."""
    cities = ["London", "Paris", "Tokyo", "New York", "Sydney"]
    return "\n".join(cities)

@mcp.prompt()
def weather_report(city: str) -> str:
    """Generate a detailed weather report prompt."""
    return f"Please provide a detailed weather report for {city}, including temperature, humidity, wind speed, and forecast."

if __name__ == "__main__":
    mcp.run()
```

## Step 3: Run the Server

```bash
uv run python server.py
# or
mcp run server.py
```

## Step 4: Test It

### With MCP Inspector
```bash
mcp dev server.py
```

### With Claude Desktop

Add to config:
```json
{
  "mcpServers": {
    "weather": {
      "command": "uv",
      "args": ["run", "python", "/path/to/server.py"]
    }
  }
}
```

## Next Steps

- Add real API calls
- Implement error handling
- Add more tools and resources
- Deploy as a remote server with Streamable HTTP
