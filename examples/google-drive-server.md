# Example: Google Drive MCP Server

The Google Drive MCP server enables AI models to search, read, and manage files in Google Drive.

## Configuration

```json
{
  "mcpServers": {
    "google-drive": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-google-drive"],
      "env": {
        "GOOGLE_CLIENT_ID": "your-client-id",
        "GOOGLE_CLIENT_SECRET": "your-client-secret"
      }
    }
  }
}
```

## Authentication

The server uses OAuth 2.0 to authenticate with Google:
1. On first run, opens a browser for Google sign-in
2. User grants access to Drive files
3. Tokens are stored locally for subsequent use

## Tools

| Tool | Description |
|------|-------------|
| `search_files` | Search Drive files by name or content |
| `read_file` | Read a file's content |
| `list_files` | List files in a folder |

## Resources

Files are exposed as resources using Google Drive URIs:

```
gdrive:///document/1abc2def3ghi
gdrive:///spreadsheet/4jkl5mno6pqr
```

## Example Interactions

- "Find my quarterly report in Google Drive"
- "Read the meeting notes from last Tuesday"
- "What spreadsheets do I have in the Finance folder?"

## Supported File Types

| Type | Handling |
|------|----------|
| Google Docs | Exported as Markdown |
| Google Sheets | Exported as CSV |
| Google Slides | Exported as plain text |
| PDF | Text extraction |
| Images | Returned as base64 |
| Other files | Returned as-is or base64 |

## Security Notes

- Only grant read access unless write is needed
- Tokens are stored locally — protect the token file
- Review OAuth scopes before granting access
- Consider using a service account for team setups
