# OAuth 2.0 Flow in MCP

This document details the OAuth 2.0 authorization flow used by MCP for remote server authentication.

## Discovery

The client first discovers the server's auth configuration:

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

### Step 3: Use the Token
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

## Dynamic Client Identification (2025-11-25)

The 2025-11-25 specification introduced support for Dynamic Client Registration and Client ID Metadata (CIMD).

**Note:** Dynamic Client Registration is considered an outdated process.
Developers are strongly recommended to use **Client ID Metadata (CIMD)** as the preferred method for dynamic client identification.

Instead of using a static string for `client_id`, clients can use a URL that points to a metadata document.

### Client ID Metadata Document

The metadata document is a JSON file that describes the client.
The Authorization Server fetches this document to verify the client's identity and configuration.

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

### Metadata Usage in the Flow

When using dynamic identification, the `client_id` in the authorization request is the URL of the metadata document.

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

The Authorization Server will fetch the metadata from the provided URL, validate it, and use the information (like `redirect_uris`) for the flow.

## PKCE

MCP requires **PKCE** (Proof Key for Code Exchange) for all authorization code flows, even for confidential clients.
