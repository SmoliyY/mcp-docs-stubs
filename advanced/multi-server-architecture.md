# Multi-Server Architecture

Production MCP deployments typically involve multiple servers, each handling a specific domain. This guide covers patterns for multi-server setups.

## Architecture Pattern

```
Host Application
├── MCP Client → Filesystem Server
├── MCP Client → GitHub Server
├── MCP Client → Database Server
└── MCP Client → Slack Server
```

Each server runs independently with its own process and connection.

## Tool Aggregation

The host collects tools from all servers and presents them as a unified set to the LLM:

```
All Available Tools:
├── [filesystem] read_file, write_file, search_files
├── [github] create_issue, list_prs, merge_pr
├── [database] query, list_tables
└── [slack] send_message, read_messages
```

## Name Conflicts

When multiple servers define tools with the same name, hosts can:

1. **Prefix with server name**: `filesystem_read_file` vs `github_read_file`
2. **Use namespaces**: `filesystem/read_file`
3. **Priority ordering**: First registered server wins
4. **User selection**: Ask the user which server to use

## Cross-Server Workflows

The LLM can orchestrate workflows spanning multiple servers:

1. Read a file → **Filesystem Server**
2. Create an issue about it → **GitHub Server**
3. Notify the team → **Slack Server**

The LLM decides the sequence and passes data between tool calls.

## Resource Considerations

- Each server has its own **session state**
- Servers **cannot communicate** with each other directly
- The host manages **connection lifecycle** for all servers
- **Failures are isolated** — one server crashing doesn't affect others

## Performance Tips

- Initialize servers in **parallel**
- Cache tool listings
- Use **lazy connection** — connect when first needed
- Monitor per-server latency and health
