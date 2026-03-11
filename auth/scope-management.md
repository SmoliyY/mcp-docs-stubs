# OAuth Scope Management

Scopes control what actions an MCP client can perform on behalf of the user. Proper scope management is essential for security.

## What Are Scopes?

Scopes are strings that represent specific permissions:

```
read          — Read data
write         — Modify data
admin         — Administrative actions
tools:execute — Execute server tools
resources:read — Read server resources
```

## Requesting Scopes

During OAuth authorization, the client requests specific scopes:

```
GET /authorize?
  scope=read+write+tools:execute
  ...
```

## Default Scopes (SEP-835)

The 2025-11-25 specification introduced default scope definitions:

```json
{
  "scopes_supported": ["read", "write", "admin"],
  "default_scopes": ["read"]
}
```

If a client doesn't request specific scopes, the default scopes are applied.

## Incremental Consent

Introduced in 2025-11-25, servers can request additional scopes mid-session:

```json
{
  "jsonrpc": "2.0",
  "method": "auth/requestScopes",
  "params": {
    "scopes": ["write", "admin"],
    "reason": "The delete operation requires admin permissions"
  }
}
```

The client prompts the user to grant the additional permissions.

## Best Practices

### For Servers
- Define granular scopes (not just "full_access")
- Document what each scope enables
- Request minimum scopes initially
- Use incremental consent for elevated permissions

### For Clients
- Show users what scopes are requested
- Explain what each scope means
- Allow users to deny individual scopes
- Track granted scopes per server

## Scope Naming Convention

```
domain:action:resource

Examples:
  files:read:*
  files:write:documents
  db:query:users
  github:write:issues
```
