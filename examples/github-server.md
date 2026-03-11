# Example: GitHub MCP Server

The GitHub MCP server allows AI models to interact with GitHub repositories, issues, pull requests, and more.

## Installation

```bash
npx -y @modelcontextprotocol/server-github
```

## Configuration

```json
{
  "mcpServers": {
    "github": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-github"],
      "env": {
        "GITHUB_TOKEN": "ghp_your_personal_access_token"
      }
    }
  }
}
```

## Available Tools

### Repository Operations
| Tool | Description |
|------|-------------|
| `search_repositories` | Search GitHub repositories |
| `get_file_contents` | Read file from a repository |
| `create_or_update_file` | Create or update a file |
| `push_files` | Push multiple files in one commit |

### Issue Operations
| Tool | Description |
|------|-------------|
| `list_issues` | List issues for a repository |
| `create_issue` | Create a new issue |
| `update_issue` | Update an existing issue |
| `add_issue_comment` | Comment on an issue |

### Pull Request Operations
| Tool | Description |
|------|-------------|
| `list_pull_requests` | List PRs for a repository |
| `create_pull_request` | Create a new PR |
| `merge_pull_request` | Merge a PR |

### Branch Operations
| Tool | Description |
|------|-------------|
| `create_branch` | Create a new branch |
| `list_branches` | List repository branches |

## Example Usage

Once configured, you can ask Claude:

- "Create an issue in my-org/my-repo about the login bug"
- "What are the open pull requests in the project?"
- "Search GitHub for MCP server implementations in Python"

## Token Scopes

Your GitHub token needs these scopes:
- `repo` — Full repository access
- `read:org` — Read organization info
- `issues` — Issue management
