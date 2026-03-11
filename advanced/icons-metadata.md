# Icons and Metadata

The **2025-11-25** specification introduced support for **icons** on tools, resources, and prompts, improving the user experience in host applications.

## Icon Support

Servers can provide icons for their primitives, which clients display in their UI.

### Tool with Icon

```json
{
  "name": "send_email",
  "description": "Send an email message",
  "annotations": {
    "title": "Send Email"
  },
  "icon": "data:image/svg+xml;base64,PHN2ZyB4bWxucz0i...",
  "inputSchema": { ... }
}
```

### Resource with Icon

```json
{
  "uri": "db://users",
  "name": "Users Database",
  "icon": "https://example.com/icons/database.svg",
  "mimeType": "application/json"
}
```

## Icon Formats

Icons can be provided as:

| Format | Example |
|--------|---------|
| **Data URI** | `data:image/svg+xml;base64,...` |
| **HTTPS URL** | `https://example.com/icon.png` |

## Server Info Icon

Servers can also include an icon in their info:

```json
{
  "serverInfo": {
    "name": "My MCP Server",
    "version": "1.0.0",
    "icon": "data:image/png;base64,..."
  }
}
```

## Best Practices

- Use **SVG** format for scalability
- Keep icons **small** (under 10KB for data URIs)
- Use **HTTPS** URLs for larger icons
- Provide icons that are **recognizable at small sizes**
- Use consistent styling across all icons in a server
- Icons are optional — clients must handle their absence

## Client Behavior

Clients SHOULD:
- Display icons alongside tool/resource/prompt names
- Fall back to default icons when none are provided
- Cache remote icons to reduce network requests
- Sanitize data URI icons for security
