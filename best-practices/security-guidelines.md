# Security Guidelines for MCP

Security is paramount when building MCP integrations. Follow these guidelines to protect users and systems.

## For Server Developers

### Input Validation
- Validate ALL tool parameters before processing
- Sanitize file paths to prevent directory traversal
- Validate URLs to prevent SSRF attacks
- Limit string lengths to prevent buffer issues

### Least Privilege
- Request only necessary permissions
- Use read-only database connections when possible
- Limit filesystem access to specific directories
- Scope API tokens to required operations only

### Data Protection
- Never log sensitive data (passwords, tokens)
- Sanitize tool results to remove credentials
- Use environment variables for secrets, never hardcode
- Encrypt sensitive data at rest and in transit

### Output Safety
- Sanitize data before returning to prevent prompt injection
- Don't include raw HTML or scripts in text results
- Limit response sizes to prevent memory issues

## For Client Developers

### User Consent
- Show all tool calls to users before execution
- Require explicit approval for destructive operations
- Allow users to inspect tool arguments and results
- Provide an audit log of all actions

### Server Trust
- Verify server identity for remote connections
- Use allowlists for permitted servers
- Display server names clearly in the UI
- Warn users about unknown or unverified servers

### Token Security
- Use OAuth 2.0 with PKCE for remote auth
- Store tokens securely (keychain, encrypted storage)
- Implement token rotation
- Validate token scopes before use

## Common Vulnerabilities

| Vulnerability | Mitigation |
|--------------|------------|
| Prompt injection | Sandbox tool outputs, confirm actions |
| Data exfiltration | Limit tool combinations, monitor patterns |
| Tool squatting | Verify server identity, use allowlists |
| Path traversal | Validate and canonicalize paths |
| SSRF | Validate URLs, use allowlists |
