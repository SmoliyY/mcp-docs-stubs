# Example: Filesystem MCP Server

A filesystem server provides AI models with the ability to read, write, and manage files. This is one of the most common MCP server types.

## Official Server

The official filesystem server is available at:
```bash
npx -y @modelcontextprotocol/server-filesystem /path/to/allowed/directory
```

## Tools Provided

| Tool | Parameters | Description |
|------|------------|-------------|
| `read_file` | `path` | Read contents of a file (UTF-8) |
| `write_file` | `path`, `content` | Write content to a file (UTF-8) |
| `list_directory` | `path` | List files in a directory |
| `create_directory` | `path` | Create a new directory |
| `move_file` | `from`, `to` | Move or rename a file |
| `search_files` | `path`, `query` | Search for files by name pattern |
| `get_file_info` | `path` | Get file metadata (size, modified date) |

### Tool Behavior

- **Encoding**: All file operations use UTF-8 encoding by default.
- **Error Handling**: If a tool is called with a path outside the allowed directories, it returns a tool error (`isError: true`) with the message "Access denied".
- **Path Validation**: Tools expect absolute paths or paths relative to the allowed directories. Parent directories must exist before calling `write_file` or `create_directory`.

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
