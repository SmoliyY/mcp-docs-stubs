# MCP Resources

Resources are the second core primitive in MCP. They represent **data sources** that provide contextual information to AI applications.

## What Are Resources?

Resources are similar to **GET endpoints** in a REST API. They provide data without performing significant computation or causing side effects. Think of them as read-only data feeds.

## Resource Identification

Every resource is identified by a **URI**:

```
file:///home/user/documents/report.pdf
postgres://localhost/mydb/users/schema
custom://weather/current/london
```

## Resource Types

### Static Resources
Resources with known, fixed URIs. Listed directly via `resources/list`.

### Resource Templates
Dynamic resources using URI templates (RFC 6570):

```json
{
  "uriTemplate": "file:///{path}",
  "name": "File Contents",
  "description": "Read any file by path",
  "mimeType": "text/plain"
}
```

## Resource Content

Resources return content in two forms:

### Text Content
```json
{
  "uri": "file:///config.json",
  "mimeType": "application/json",
  "text": "{ \"key\": \"value\" }"
}
```

### Binary Content (Base64)
```json
{
  "uri": "file:///image.png",
  "mimeType": "image/png",
  "blob": "iVBORw0KGgo..."
}
```

## Key Characteristics

- **Application-controlled** — The host decides when to fetch resources
- **Read-only** — Resources should not cause side effects
- **URI-identified** — Standard URI scheme for addressing
- **Subscribable** — Clients can subscribe to change notifications
- **Typed** — Resources declare their MIME type
