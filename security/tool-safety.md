# Tool Safety

MCP tools can perform powerful actions. Ensuring tool safety requires cooperation between servers, clients, and users.

## Risk Levels

### Low Risk (Read-Only)
- Fetching public data
- Reading file metadata
- Listing directory contents
- Querying public APIs

### Medium Risk (State-Changing)
- Writing files
- Creating database records
- Sending messages
- Making API calls that modify state

### High Risk (Destructive)
- Deleting files or records
- Dropping database tables
- Executing arbitrary code
- Modifying system configuration

## Tool Annotations for Safety

Use annotations to communicate risk levels:

```json
{
  "name": "delete_records",
  "annotations": {
    "readOnlyHint": false,
    "destructiveHint": true,
    "idempotentHint": false,
    "openWorldHint": false
  }
}
```

## Client-Side Protections

### Auto-Approve Policies
Clients can auto-approve tools marked `readOnlyHint: true` while requiring confirmation for others.

### Allowlists and Denylists
Maintain lists of pre-approved or blocked tools per server.

### Rate Limiting
Limit how many times a tool can be called per session or time period.

## Server-Side Protections

### Input Validation
Always validate tool parameters:
- Check types and ranges
- Sanitize file paths (prevent directory traversal)
- Validate URIs and URLs
- Limit string lengths

### Audit Logging
Log every tool invocation with:
- Timestamp
- Tool name and arguments
- Caller identity
- Result summary
