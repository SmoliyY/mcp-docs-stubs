# Example: Web Search MCP Server

A web search server gives AI models the ability to search the internet and retrieve web content.

## Brave Search Server

The official Brave Search server provides web and local search capabilities.

### Configuration

```json
{
  "mcpServers": {
    "brave-search": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-brave-search"],
      "env": {
        "BRAVE_API_KEY": "your-brave-api-key"
      }
    }
  }
}
```

### Tools

| Tool | Description |
|------|-------------|
| `brave_web_search` | Search the web using Brave Search |
| `brave_local_search` | Search for local businesses and places |

## Building a Custom Search Server

```python
from mcp.server.fastmcp import FastMCP
import httpx

mcp = FastMCP("search-server")

@mcp.tool()
async def web_search(query: str, num_results: int = 5) -> str:
    """Search the web for information.

    Args:
        query: The search query
        num_results: Number of results to return (1-10)
    """
    async with httpx.AsyncClient() as client:
        response = await client.get(
            "https://api.search-provider.com/search",
            params={"q": query, "count": min(num_results, 10)},
            headers={"Authorization": f"Bearer {API_KEY}"}
        )
        results = response.json()

    formatted = []
    for r in results["items"]:
        formatted.append(f"**{r['title']}**\n{r['url']}\n{r['snippet']}")

    return "\n\n".join(formatted)
```

## Web Fetching Tool

```python
@mcp.tool()
async def fetch_webpage(url: str) -> str:
    """Fetch and extract text content from a webpage.

    Args:
        url: The URL to fetch (must be HTTPS)
    """
    if not url.startswith("https://"):
        return "Error: Only HTTPS URLs are allowed"

    async with httpx.AsyncClient() as client:
        response = await client.get(url, follow_redirects=True)
        # Convert HTML to markdown or plain text
        return extract_text(response.text)
```

## Safety Considerations

- Validate URLs to prevent SSRF
- Rate-limit search requests
- Respect robots.txt
- Set appropriate User-Agent headers
