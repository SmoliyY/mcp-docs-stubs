# Example: Docker MCP Server

A Docker MCP server allows AI models to manage Docker containers, images, and compose stacks.

## Tools

| Tool | Description |
|------|-------------|
| `list_containers` | List running and stopped containers |
| `start_container` | Start a stopped container |
| `stop_container` | Stop a running container |
| `container_logs` | Get logs from a container |
| `list_images` | List available Docker images |
| `build_image` | Build an image from a Dockerfile |
| `compose_up` | Start a docker-compose stack |
| `compose_down` | Stop a docker-compose stack |

## Implementation

```python
from mcp.server.fastmcp import FastMCP
import docker

mcp = FastMCP("docker-server")
client = docker.from_env()

@mcp.tool()
def list_containers(all: bool = False) -> str:
    """List Docker containers.

    Args:
        all: Include stopped containers (default: only running)
    """
    containers = client.containers.list(all=all)
    result = []
    for c in containers:
        result.append(f"{c.short_id} | {c.name} | {c.status} | {c.image.tags}")
    return "\n".join(result) or "No containers found"

@mcp.tool()
def container_logs(container_id: str, lines: int = 50) -> str:
    """Get recent logs from a Docker container.

    Args:
        container_id: Container ID or name
        lines: Number of log lines to return
    """
    container = client.containers.get(container_id)
    return container.logs(tail=lines).decode("utf-8")

@mcp.tool()
def stop_container(container_id: str) -> str:
    """Stop a running Docker container.

    Args:
        container_id: Container ID or name
    """
    container = client.containers.get(container_id)
    container.stop()
    return f"Container {container_id} stopped"
```

## Security Warning

Docker access is powerful — a container with host mount access can read/write the entire filesystem. Use with caution and limit what the AI can do.

## Configuration

```json
{
  "mcpServers": {
    "docker": {
      "command": "python",
      "args": ["docker_server.py"]
    }
  }
}
```
