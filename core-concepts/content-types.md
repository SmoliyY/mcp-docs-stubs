# Content Types in MCP

MCP supports multiple content types for tool results, resources, and prompt messages.

## Text Content

The most common content type. Used for plain text, JSON, markdown, code, and any text-based data.

```json
{
  "type": "text",
  "text": "Hello, World!"
}
```

### With Annotations
```json
{
  "type": "text",
  "text": "Important result",
  "annotations": {
    "audience": ["user"],
    "priority": 1.0
  }
}
```

## Image Content

For screenshots, charts, diagrams, and other visual data. Images are Base64-encoded.

```json
{
  "type": "image",
  "data": "iVBORw0KGgoAAAANSUhEUgAA...",
  "mimeType": "image/png"
}
```

Supported MIME types:
- `image/png`
- `image/jpeg`
- `image/gif`
- `image/svg+xml`
- `image/webp`

## Resource Content (Embedded)

Tool results can embed resource references:

```json
{
  "type": "resource",
  "resource": {
    "uri": "file:///output/report.csv",
    "mimeType": "text/csv",
    "text": "name,age\nAlice,30\nBob,25"
  }
}
```

## Content Annotations

Annotations provide metadata about content:

| Annotation | Type | Description |
|-----------|------|-------------|
| `audience` | string[] | Who should see this (`user`, `assistant`) |
| `priority` | number | Importance (0.0 to 1.0) |

### Audience Control
- `["user"]` — Show to the user, not the LLM
- `["assistant"]` — Send to the LLM, don't display to user
- `["user", "assistant"]` — Both see it (default)

## Multiple Content Blocks

Tool results can return arrays of mixed content:

```json
{
  "content": [
    { "type": "text", "text": "Analysis complete:" },
    { "type": "image", "data": "...", "mimeType": "image/png" },
    { "type": "text", "text": "3 issues found." }
  ]
}
```
