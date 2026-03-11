# Example: Filesystem MCP Server

A filesystem server provides AI models with the ability to read, write, and manage files. This is one of the most common MCP server types.

## Official Server

The official filesystem server is available at:
```bash
npx -y @modelcontextprotocol/server-filesystem /path/to/allowed/directory
```

## Tools Provided

| Tool | Description |
|------|-------------|
| `read_file` | Read contents of a file |
| `write_file` | Write content to a file |
| `list_directory` | List files in a directory |
| `create_directory` | Create a new directory |
| `move_file` | Move or rename a file |
| `search_files` | Search for files by name pattern |
| `get_file_info` | Get file metadata (size, modified date) |

## Resources Provided

The server exposes files as resources:

```
file:///path/to/file.txt
```

## Configuration

```json
{
  "mcpServers": {
    "filesystem": {
      "command": "npx",
      "args": [
        "-y",
        "@modelcontextprotocol/server-filesystem",
        "/home/user/documents",
        "/home/user/projects"
      ]
    }
  }
}
```

Multiple directories can be specified as allowed paths.

## Security Features

- **Path validation** — Only allows access within specified directories
- **No directory traversal** — Blocks `..` in paths
- **Symlink resolution** — Follows symlinks but validates the target
- **Read-only mode** — Can be configured to disable writes

## Building Your Own

```typescript
server.tool("read_file", "Read a file's contents", {
  path: z.string().describe("Absolute file path")
}, async ({ path }) => {
  if (!isWithinAllowedDirs(path)) {
    return { content: [{ type: "text", text: "Access denied" }], isError: true };
  }
  const content = await fs.readFile(path, "utf-8");
  return { content: [{ type: "text", text: content }] };
});
```
