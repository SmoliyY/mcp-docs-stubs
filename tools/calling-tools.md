# Calling MCP Tools

Once a client knows about available tools, the LLM can invoke them via `tools/call` requests.

## Request Format

```json
{
  "jsonrpc": "2.0",
  "method": "tools/call",
  "params": {
    "name": "get_weather",
    "arguments": {
      "city": "Tokyo",
      "units": "celsius"
    }
  },
  "id": 3
}
```

## Success Response

```json
{
  "jsonrpc": "2.0",
  "id": 3,
  "result": {
    "content": [
      {
        "type": "text",
        "text": "Tokyo: 22°C, sunny with light clouds"
      }
    ]
  }
}
```

## Error Response (Tool-Level)

Tool errors are reported within the result, not as JSON-RPC errors:

```json
{
  "jsonrpc": "2.0",
  "id": 3,
  "result": {
    "content": [
      {
        "type": "text",
        "text": "Error: City 'Atlantis' not found"
      }
    ],
    "isError": true
  }
}
```

## Content Types in Results

Tools can return multiple content types:

### Text
```json
{ "type": "text", "text": "Result text here" }
```

### Image
```json
{ "type": "image", "data": "base64...", "mimeType": "image/png" }
```

### Embedded Resource
```json
{
  "type": "resource",
  "resource": {
    "uri": "file:///output.csv",
    "text": "col1,col2\nval1,val2",
    "mimeType": "text/csv"
  }
}
```

## User Confirmation

Hosts SHOULD display tool calls to users and obtain confirmation before executing, especially for tools with `destructiveHint: true`.
