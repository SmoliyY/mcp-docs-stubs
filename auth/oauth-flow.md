# OAuth 2.0 Flow

MCP uses the standard OAuth 2.0 authorization code flow to authenticate with remote servers.
This process allows a client to safely get an access token to talk to an MCP server.

## Discovery

First, the client needs to find out where the authorization server is.
The client makes a request to the MCP server's discovery endpoint:

```http
GET /.well-known/oauth-authorization-server HTTP/1.1
Host: mcp-server.example.com
```

The server responds with the endpoints and capabilities it supports:

```json
{
  "issuer": "https://auth.example.com",
  "authorization_endpoint": "https://auth.example.com/authorize",
  "token_endpoint": "https://auth.example.com/token",
  "scopes_supported": ["read", "write", "admin"],
  "response_types_supported": ["code"],
  "grant_types_supported": ["authorization_code", "refresh_token"]
}
```

## Authorization Code Flow

### Step 1: Authorization Request

The client directs the user to the authorization server.
This request includes the client ID and the requested scopes.

```
GET /authorize?
  response_type=code&
  client_id=my-mcp-client&
  redirect_uri=http://localhost:3000/callback&
  scope=read+write&
  state=xyz123&
  code_challenge=E9Melhoa2OwvFrEMTJguCHaoeK1t8URWbuGJSstw-cM&
  code_challenge_method=S256&
  resource=https://mcp-server.example.com
```

### Step 2: Token Exchange

After the user approves the request, the client receives an authorization code.
The client then exchanges this code for an access token.

```http
POST /token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&
code=AUTH_CODE_HERE&
redirect_uri=http://localhost:3000/callback&
client_id=my-mcp-client&
code_verifier=dBjftJeZ4CVP-mB92K27uhbUJU1p1r_wW1gFWFOEjXk
```

### Step 3: Resource Request

Now the client can use the access token to make requests to the MCP server.
The token goes in the `Authorization` header.

```http
POST /mcp HTTP/1.1
Authorization: Bearer <access_token>
Content-Type: application/json

{"jsonrpc":"2.0","method":"tools/list","id":1}
```

## Token Refresh

Access tokens eventually expire.
If the client received a refresh token, it can use it to get a new access token without involving the user again.

```http
POST /token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&
refresh_token=<refresh_token>&
client_id=my-mcp-client
```

## Dynamic Client Identification

The November 2025 update added support for Dynamic Client Registration and Client ID Metadata (CIMD).

**Warning:** Dynamic Client Registration is considered legacy.
You should use Client ID Metadata (CIMD) for dynamic client identification.

With CIMD, clients can provide a URL for the `client_id` that points to a metadata document instead of using a static string.

### Client ID Metadata Document

The metadata document is a JSON file that describes your client.
The authorization server fetches this document to verify who the client is and what it is allowed to do.

Example metadata document (`https://client.example.com/mcp-client.json`):

```json
{
  "client_id": "https://client.example.com/mcp-client.json",
  "client_name": "Example MCP Client",
  "client_uri": "https://client.example.com",
  "logo_uri": "https://client.example.com/logo.png",
  "redirect_uris": ["https://client.example.com/callback"],
  "grant_types": ["authorization_code", "refresh_token"],
  "response_types": ["code"],
  "scope": "read write"
}
```

### Metadata Usage

When you use dynamic identification, set the `client_id` in the authorization request to the URL of your metadata document.

```
GET /authorize?
  response_type=code&
  client_id=https://client.example.com/mcp-client.json&
  redirect_uri=https://client.example.com/callback&
  scope=read+write&
  state=xyz123&
  code_challenge=E9Melhoa2OwvFrEMTJguCHaoeK1t8URWbuGJSstw-cM&
  code_challenge_method=S256&
  resource=https://mcp-server.example.com
```

The authorization server will fetch the metadata from that URL, check that everything is correct, and make sure the `redirect_uris` match.

## PKCE

MCP requires Proof Key for Code Exchange (PKCE) for all authorization code flows.
This adds an extra layer of security, even for confidential clients.
