# OAuth 2.0 Flow in MCP

This document details the OAuth 2.0 authorization flow used by MCP for remote server authentication.

## Specification versions

The OAuth 2.0 flow and its subsequent enhancements were introduced and refined in the following MCP specifications:
- **2025-06-18**: Introduced the classification of MCP servers as OAuth Resource Servers and added support for **Resource Indicators (RFC 8707)**.
- **2025-11-25**: Added support for **OpenID Connect Discovery**, **Incremental Scope Consent**, and **OAuth Client ID Metadata**.

Understanding these versions helps ensure compatibility between MCP clients and servers.

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
