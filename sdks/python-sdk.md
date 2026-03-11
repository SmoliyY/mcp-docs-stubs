# Python SDK

The official Python SDK for MCP features **FastMCP**, a high-level framework that uses decorators and type hints to simplify server development.

## Installation

```bash
pip install mcp
# or
uv add mcp
```

## Quick Start — Server with FastMCP

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("my-server")

@mcp.tool()
def add(a: int, b: int) -> int:
    """Add two numbers together."""
    return a + b

@mcp.resource("config://app")
def get_config() -> str:
    """Return application configuration."""
    return json.dumps({"debug": False, "version": "1.0"})

@mcp.prompt()
def review_code(language: str) -> str:
    """Generate a code review prompt."""
    return f"Please review the following {language} code for issues:"

if __name__ == "__main__":
    mcp.run()
```

## Running the Server

```bash
# stdio transport (default)
python server.py

# or with the mcp CLI
mcp run server.py

# HTTP transport
mcp run server.py --transport streamable-http --port 8000
```

## FastMCP Features

- **Automatic schema generation** from type hints
- **Decorator-based** tool/resource/prompt registration
- **Built-in validation** using Python types
- **Context manager** for accessing MCP features within tools
- **Image support** via the `Image` class

## Low-Level API

For advanced use cases, the SDK also exposes a low-level `Server` class:

```python
from mcp.server import Server
from mcp.server.stdio import stdio_server

server = Server("my-server")

@server.list_tools()
async def list_tools():
    return [...]
```

## Repository

`github.com/modelcontextprotocol/python-sdk`
