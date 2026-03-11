# Example: Slack MCP Server

A Slack MCP server enables AI models to interact with Slack workspaces — reading messages, posting updates, and managing channels.

## Configuration

```json
{
  "mcpServers": {
    "slack": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-slack"],
      "env": {
        "SLACK_BOT_TOKEN": "xoxb-your-bot-token",
        "SLACK_TEAM_ID": "T01234567"
      }
    }
  }
}
```

## Typical Tools

| Tool | Description |
|------|-------------|
| `send_message` | Send a message to a channel |
| `list_channels` | List available channels |
| `read_messages` | Read recent messages from a channel |
| `reply_to_thread` | Reply to a specific thread |
| `search_messages` | Search messages across channels |
| `add_reaction` | Add an emoji reaction |

## Building a Custom Slack Server

```python
from mcp.server.fastmcp import FastMCP
from slack_sdk import WebClient

mcp = FastMCP("slack-server")
client = WebClient(token=os.environ["SLACK_BOT_TOKEN"])

@mcp.tool()
def send_message(channel: str, text: str) -> str:
    """Send a message to a Slack channel.

    Args:
        channel: Channel name (without #) or channel ID
        text: Message text (supports Slack markdown)
    """
    result = client.chat_postMessage(channel=channel, text=text)
    return f"Message sent to #{channel} (ts: {result['ts']})"

@mcp.tool()
def read_messages(channel: str, limit: int = 10) -> str:
    """Read recent messages from a Slack channel.

    Args:
        channel: Channel name or ID
        limit: Number of messages to fetch (max 100)
    """
    result = client.conversations_history(
        channel=channel, limit=min(limit, 100)
    )
    messages = [
        f"{m.get('user', 'bot')}: {m['text']}"
        for m in result["messages"]
    ]
    return "\n".join(reversed(messages))
```

## Required Bot Permissions

- `channels:read` — View channel info
- `channels:history` — Read messages
- `chat:write` — Post messages
- `reactions:write` — Add reactions
- `search:read` — Search messages
