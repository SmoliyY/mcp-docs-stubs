# MCP Authentication and Authorization

MCP uses **OAuth 2.0** for authentication and authorization of remote servers.
This was significantly enhanced in the 2025-06-18 and 2025-11-25 specifications.

## When Auth Is Needed

- **stdio transport**: Usually no auth needed (local process, inherits user's permissions)
- **Streamable HTTP**: Auth required for remote/multi-tenant servers

## OAuth 2.0 Flow

MCP servers act as **OAuth Resource Servers**.
The authorization flow:

1. Client discovers the server's auth requirements
2. Client obtains an access token from the authorization server
3. Client includes the token in requests via the `Authorization` header

```http
POST /mcp HTTP/1.1
Authorization: Bearer <access_token>
Content-Type: application/json
```

## Key Auth Features

### Resource Indicators (RFC 8707)
Clients use resource indicators to specify which MCP server the token is intended for.
This prevents a malicious server from using tokens meant for another server.

### Incremental Scope Consent (2025-11-25)
Servers can request additional permissions mid-session without requiring a full re-authorization flow.

### OpenID Connect Discovery (2025-11-25)
Servers can use OIDC Discovery to advertise their auth configuration, simplifying client setup.

### Dynamic Client Identification (2025-11-25)
MCP supports Dynamic Client Registration and Client ID Metadata (CIMD).
While Dynamic Client Registration is supported, it is considered an outdated process.
**Client ID Metadata (CIMD)** is the preferred method for dynamic client identification.
These features allow clients to identify themselves using metadata documents instead of static client IDs.
This simplifies the onboarding process for new clients and servers.

## Authorization Server Metadata

Servers expose their auth requirements at:
```
GET /.well-known/oauth-authorization-server
```

## Security Requirements

- Clients MUST validate tokens before use
- Servers MUST verify tokens on every request
- Tokens SHOULD have limited scopes and lifetimes
- HTTPS is required for all auth-related communication
