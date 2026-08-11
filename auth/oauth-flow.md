# OAuth 2.0 Flow

MCP implements the OAuth 2.0 authorization code flow for remote server authentication.

## Discovery

Discovery procedure:

```http
GET /.well-known/oauth-authorization-server HTTP/1.1
Host: mcp-server.example.com
```

Response:

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

```http
POST /mcp HTTP/1.1
Authorization: Bearer <access_token>
Content-Type: application/json

{"jsonrpc":"2.0","method":"tools/list","id":1}
```

## Token Refresh

```http
POST /token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&
refresh_token=<refresh_token>&
client_id=my-mcp-client
```

## Dynamic Client Identification

The 2025-11-25 specification introduced support for Dynamic Client Registration and Client ID Metadata (CIMD).

**Warning:** Dynamic Client Registration is legacy.
Implementations SHOULD use Client ID Metadata (CIMD) for dynamic client identification.

Clients MAY provide a URL for `client_id` that references a metadata document instead of using a static string.

### Client ID Metadata Document

The metadata document is a JSON file describing the client.
The Authorization Server retrieves this document to verify client identity and configuration.

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

When using dynamic identification, the `client_id` in the authorization request MUST be the URL of the metadata document.

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

The Authorization Server fetches the metadata from the `client_id` URL, validates the content, and enforces the registered `redirect_uris`.

## PKCE

MCP requires Proof Key for Code Exchange (PKCE) for all authorization code flows, including those for confidential clients.
