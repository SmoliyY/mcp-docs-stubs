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

### Error Responses

If the authorization request is invalid or the user denies the request, the server redirects back to the `redirect_uri` with error parameters.
For example, if the requested scope is unsupported, the server returns an `invalid_scope` error:

```
GET /callback?
  error=invalid_scope&
  error_description=The+requested+scope+is+invalid+or+unsupported.&
  state=xyz123
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

The server responds with an access token and the list of granted scopes.
Clients must check the `scope` field in the response rather than assuming all requested scopes were granted.

```json
{
  "access_token": "token_example_123",
  "token_type": "Bearer",
  "expires_in": 3600,
  "refresh_token": "refresh_example_456",
  "scope": "read"
}
```

### Step 3: Use the Token
```http
POST /mcp HTTP/1.1
Authorization: Bearer access_token_here
Content-Type: application/json

{"jsonrpc":"2.0","method":"tools/list","id":1}
```

## Token Refresh

```http
POST /token HTTP/1.1
Content-Type: application/x-www-form-urlencoded

grant_type=refresh_token&
refresh_token=REFRESH_TOKEN_HERE&
client_id=my-mcp-client
```

## PKCE

MCP requires **PKCE** (Proof Key for Code Exchange) for all authorization code flows, even for confidential clients.
