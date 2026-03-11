# Listing and Reading Resources

Clients interact with resources through listing, reading, and subscribing operations.

## Listing Resources

### List Static Resources

```json
{
  "jsonrpc": "2.0",
  "method": "resources/list",
  "id": 1
}
```

Response:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "result": {
    "resources": [
      {
        "uri": "file:///project/README.md",
        "name": "Project README",
        "description": "Main project documentation",
        "mimeType": "text/markdown"
      }
    ]
  }
}
```

### List Resource Templates

```json
{
  "jsonrpc": "2.0",
  "method": "resources/templates/list",
  "id": 2
}
```

Response:

```json
{
  "jsonrpc": "2.0",
  "id": 2,
  "result": {
    "resourceTemplates": [
      {
        "uriTemplate": "db://users/{userId}",
        "name": "User Record",
        "description": "Fetch a user by ID",
        "mimeType": "application/json"
      }
    ]
  }
}
```

## Reading Resources

```json
{
  "jsonrpc": "2.0",
  "method": "resources/read",
  "params": {
    "uri": "file:///project/README.md"
  },
  "id": 3
}
```

Response:

```json
{
  "jsonrpc": "2.0",
  "id": 3,
  "result": {
    "contents": [
      {
        "uri": "file:///project/README.md",
        "mimeType": "text/markdown",
        "text": "# My Project\n\nWelcome to the project..."
      }
    ]
  }
}
```

## Pagination

Both listing methods support cursor-based pagination using the `cursor` parameter.
