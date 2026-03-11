# Resource Templates

Resource templates allow MCP servers to expose **dynamic resources** using parameterized URIs based on RFC 6570.

## What Are Resource Templates?

While static resources have fixed URIs, templates define patterns that accept parameters. This lets servers expose a potentially infinite set of resources without listing each one individually.

## Template Syntax

Templates use curly brace placeholders:

```
db://users/{userId}
file:///{path}
api://repos/{owner}/{repo}/issues/{issueNumber}
```

## Defining Templates

### TypeScript

```typescript
server.resource(
  "user-profile",
  new ResourceTemplate("db://users/{userId}", { list: undefined }),
  async (uri, { userId }) => ({
    contents: [{
      uri: uri.href,
      mimeType: "application/json",
      text: JSON.stringify(await getUser(userId))
    }]
  })
);
```

### Python

```python
@mcp.resource("db://users/{user_id}")
def get_user(user_id: str) -> str:
    """Fetch a user profile by ID."""
    user = db.get_user(user_id)
    return json.dumps(user)
```

## Listing Templates

Clients discover templates via `resources/templates/list`:

```json
{
  "resourceTemplates": [
    {
      "uriTemplate": "db://users/{userId}",
      "name": "User Profile",
      "description": "Get user profile by ID",
      "mimeType": "application/json"
    }
  ]
}
```

## Reading Templated Resources

Clients fill in the template parameters and use `resources/read` with the complete URI:

```json
{
  "method": "resources/read",
  "params": {
    "uri": "db://users/12345"
  }
}
```

## Template vs Tool

Use a **template** when providing read-only data. Use a **tool** when the operation has side effects or requires complex computation.
