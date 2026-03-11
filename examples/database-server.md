# Example: Database MCP Server

Database MCP servers let AI models query and interact with databases. Several official and community servers exist for different databases.

## PostgreSQL Server

### Configuration

```json
{
  "mcpServers": {
    "postgres": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-postgres"],
      "env": {
        "DATABASE_URL": "postgres://user:password@localhost:5432/mydb"
      }
    }
  }
}
```

### Tools

| Tool | Description |
|------|-------------|
| `query` | Execute a read-only SQL query |
| `list_tables` | List all tables in the database |
| `describe_table` | Get table schema and columns |

## SQLite Server

### Configuration

```json
{
  "mcpServers": {
    "sqlite": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-sqlite", "/path/to/database.db"]
    }
  }
}
```

## Building a Custom Database Server

```python
from mcp.server.fastmcp import FastMCP
import asyncpg

mcp = FastMCP("my-db-server")

@mcp.tool()
async def query(sql: str) -> str:
    """Execute a read-only SQL query against the database.
    Only SELECT statements are allowed.
    """
    if not sql.strip().upper().startswith("SELECT"):
        return "Error: Only SELECT queries are allowed"

    conn = await asyncpg.connect(DATABASE_URL)
    try:
        rows = await conn.fetch(sql)
        return json.dumps([dict(r) for r in rows], default=str)
    finally:
        await conn.close()
```

## Security Considerations

- **Read-only by default** — Only allow SELECT queries
- **Parameterized queries** — Prevent SQL injection
- **Connection limits** — Pool and limit connections
- **Timeouts** — Set query execution timeouts
- **Row limits** — Cap the number of returned rows
