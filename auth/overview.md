# Authentication and Authorization

MCP utilizes OAuth 2.0 for authentication and authorization of remote servers.
Authentication capabilities were updated in the 2025-06-18 and 2025-11-25 specifications.

## Authentication Requirements

- **stdio transport**: Typically bypasses authentication as it runs as a local process inheriting host permissions.
- **Streamable HTTP**: Requires authentication for remote or multi-tenant environments.

## OAuth 2.0 Flow

MCP servers function as OAuth Resource Servers.
The authorization process:

1. Discovery of server authorization requirements.
2. Acquisition of an access token from the Authorization Server.
3. Inclusion of the token in the `Authorization` header of MCP requests.

```http
POST /mcp HTTP/1.1
Authorization: Bearer <access_token>
Content-Type: application/json
```

## Key Features

### Resource Indicators (RFC 8707)

Clients use resource indicators to target specific MCP servers.
This mechanism prevents token misuse across different servers.

### Incremental Scope Consent (2025-11-25)

Servers MAY request additional permissions mid-session without full re-authorization.

### OpenID Connect Discovery (2025-11-25)

Servers MAY use OIDC Discovery to advertise authentication configurations.

### Dynamic Client Identification (2025-11-25)

MCP supports Dynamic Client Registration and Client ID Metadata (CIMD).
Dynamic Client Registration is legacy; use Client ID Metadata (CIMD) for dynamic identification.
CIMD allows clients to identify using metadata documents instead of static client identifiers.

## Authorization Server Metadata

Servers expose authorization requirements at:

```
GET /.well-known/oauth-authorization-server
```

## Security Requirements

- Clients MUST validate tokens before use.
- Servers MUST verify tokens on every request.
- Tokens SHOULD have limited scopes and lifetimes.
- HTTPS is REQUIRED for all authentication-related communication.
