# Resource Indicators (RFC 8707)

Resource Indicators are a critical security feature in MCP that prevent **token misuse** across servers.

## The Problem

Without resource indicators, a malicious MCP server could:

1. Trick a client into authenticating
2. Receive an access token
3. Use that token to access a **different** MCP server

This is known as a **confused deputy attack**.

## The Solution

Resource Indicators (RFC 8707) bind access tokens to a specific target server.

### During Authorization

The client includes the `resource` parameter:

```
GET /authorize?
  ...
  resource=https://mcp-server.example.com
```

### During Token Exchange

```http
POST /token
Content-Type: application/x-www-form-urlencoded

grant_type=authorization_code&
code=AUTH_CODE&
resource=https://mcp-server.example.com
```

### Result

The issued token is scoped to `https://mcp-server.example.com` and will be rejected by any other server.

## How It Works

```
Client                  Auth Server              MCP Server A
  │                         │                         │
  │ authorize(resource=A)   │                         │
  ├────────────────────────►│                         │
  │                         │                         │
  │ token(audience=A)       │                         │
  │◄────────────────────────┤                         │
  │                         │                         │
  │ request + token         │                         │
  ├──────────────────────────────────────────────────►│
  │                         │                         │
  │                    ✓ Token valid for Server A      │
```

## Requirements

- Clients MUST include `resource` when requesting tokens for MCP servers
- Authorization servers SHOULD support RFC 8707
- MCP servers MUST validate the token's intended audience
- Tokens without proper audience SHOULD be rejected
