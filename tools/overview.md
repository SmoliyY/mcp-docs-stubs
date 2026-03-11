# MCP Tools

Tools are the most commonly used MCP primitive. They represent **executable functions** that an AI model can invoke to perform actions in the real world.

## What Are Tools?

Tools let LLMs interact with external systems. When a model encounters a task that requires external action — searching the web, reading a file, creating a database record — it calls a tool.

## Tool Definition

Each tool is defined with:

- **name** — Unique identifier (e.g., `search_files`)
- **description** — Human-readable explanation the LLM uses to decide when to use the tool
- **inputSchema** — JSON Schema defining the expected parameters

```json
{
  "name": "get_weather",
  "description": "Get the current weather for a specified city",
  "inputSchema": {
    "type": "object",
    "properties": {
      "city": {
        "type": "string",
        "description": "The city name"
      },
      "units": {
        "type": "string",
        "enum": ["celsius", "fahrenheit"],
        "description": "Temperature units"
      }
    },
    "required": ["city"]
  }
}
```

## Tool Results

Tools return an array of content blocks:

```json
{
  "content": [
    { "type": "text", "text": "London: 15°C, partly cloudy" }
  ],
  "isError": false
}
```

Content types include `text`, `image`, and `resource` (embedded resources).

## Key Characteristics

- **Model-controlled**: The LLM decides when to call tools
- **Side effects allowed**: Tools can modify state
- **Require user approval**: Hosts should confirm before execution
- **Discoverable**: Clients can list available tools dynamically
