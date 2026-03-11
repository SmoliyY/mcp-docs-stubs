# URI Schemes in MCP

Resources in MCP are identified by **URIs** (Uniform Resource Identifiers). Understanding URI schemes is important for resource design.

## Standard Schemes

### `file://` — Local Files
```
file:///home/user/documents/report.md
file:///C:/Users/user/docs/report.md    (Windows)
```

### `https://` — Web Resources
```
https://api.example.com/data/users
```

## Custom Schemes

MCP servers can define custom URI schemes for their resources:

### Database Resources
```
postgres://localhost/mydb/users/schema
sqlite:///path/to/db/users
db://production/orders/recent
```

### Application-Specific
```
github://owner/repo/issues/123
slack://workspace/channel/general
jira://project/PROJ-123
```

### Internal
```
memory://knowledge-graph
config://app/settings
cache://session/data
```

## URI Templates (RFC 6570)

Templates use curly braces for parameters:

```
file:///{path}
db://users/{userId}
github://{owner}/{repo}/issues/{number}
api://v2/{endpoint}?filter={filter}
```

## Best Practices

### Do
- Use descriptive, hierarchical schemes
- Keep URIs human-readable
- Follow existing conventions where possible
- Use lowercase for scheme names

### Don't
- Use opaque identifiers: `resource://abc123`
- Include credentials in URIs: `db://user:pass@host`
- Use overly complex query strings
- Create ambiguous URI patterns

## URI Resolution

When a client requests a resource, the server:
1. Parses the URI
2. Matches against registered resources or templates
3. Extracts template parameters (if applicable)
4. Fetches and returns the content
