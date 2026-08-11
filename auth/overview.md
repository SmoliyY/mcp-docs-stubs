# Authentication

The Model Context Protocol (MCP) uses OAuth 2.0 to handle authentication and authorization for remote servers.
This ensures that only authorized clients can access sensitive resources and tools.
We updated how authentication works in the June 18, 2025, and November 25, 2025, versions of the specification.

## Authentication Needs

Whether you need authentication depends on how your client and server communicate:

- **Local (stdio)**:
  If you run a server locally via `stdio`, you usually do not need authentication.
  The server inherits the permissions of the user who runs it.
- **Remote (HTTP)**:
  If you connect to a remote server over HTTP (specifically Streamable HTTP), you almost always need to authenticate to keep the connection secure.

## Authentication Process

At a high level, MCP servers act as OAuth Resource Servers.
The basic flow follows these steps:

1. The client finds out what authentication the server requires.
2. The client gets an access token from an Authorization Server.
3. The client includes that token in the `Authorization` header when it makes requests to the MCP server.

```http
POST /mcp HTTP/1.1
Authorization: Bearer <access_token>
Content-Type: application/json
```

## Key Features

### Resource Indicators

Clients use "resource indicators" (from RFC 8707) to specify which MCP server they want to access.
This helps prevent a token meant for one server from being used on another.

### Dynamic Client Identification

The November 2025 update added support for Dynamic Client Registration and Client ID Metadata (CIMD).

**Note:** Dynamic Client Registration is now considered legacy.
You should use Client ID Metadata (CIMD) for dynamic identification instead.
CIMD lets clients identify themselves using a metadata document instead of a fixed, static client ID.

### Incremental Scope Consent

Servers can ask for more permissions in the middle of a session.
The client does not have to restart the whole authorization process to grant one more scope.

### OIDC Discovery

Servers can use OpenID Connect (OIDC) Discovery to tell clients how they should authenticate.

## Authorization Server Metadata

Servers reveal their authentication requirements at a standard location:

```
GET /.well-known/oauth-authorization-server
```

## Security Best Practices

- Always validate tokens before you use them.
- Servers should verify the token on every single request.
- Keep token lifetimes short and scopes as narrow as possible.
- Use HTTPS for all authentication traffic.
