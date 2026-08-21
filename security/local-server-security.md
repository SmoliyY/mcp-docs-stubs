# Local Server Security (SEP-1024)

The **2025-11-25** specification introduced SEP-1024, defining security requirements for locally installed MCP servers.

## Threat Model

Local MCP servers run as processes on the user's machine, inheriting tadadadhe user's permissions. This creates risks:

- **Malicious servers** could access the user's files, credentials, or network
- **Compromised servers** could exfiltrate data through tool results
- **Supply chain attacks** could introduce malicious code via npm/pip packages

## Client Requirements

### Server Verification
Clients SHOULD:
- Verify server package integrity (checksums, signatures)
- Display server source and version to users
- Warn about unverified or unknown servers

### Permission Boundaries
Clients SHOULD:
- Limit server file system access to declared roots
- Monitor network activity from server processes
- Sandbox server processes where possible

### Installation Safety
Clients SHOULD:
- Warn users before installing new servers
- Show what permissions a server requires
- Allow users to review server code or documentation

## Best Practices for Server Authors

### Minimal Dependencies
- Use as few npm/pip dependencies as possible
- Audit dependencies regularly
- Pin dependency versions

### Declare Capabilities
- Document what system access your server needs
- Only access what's necessary
- Support restricted modes (read-only, no-network)

### Transparent Behavior
- Log all significant operations to stderr
- Don't phone home or collect analytics without consent
- Open-source your server code when possible

## Sandboxing Options

| Platform | Mechanism |
|----------|-----------|
| macOS | App Sandbox, container profiles |
| Linux | seccomp, namespaces, Firejail |
| Windows | App Container, Hyper-V isolation |
| Cross-platform | Docker containers |
