# Debugging MCP Clients

When building or troubleshooting an MCP client, these techniques help identify connection and communication issues.

## Connection Diagnostics

### Check Server Availability
```typescript
try {
  await client.connect(transport);
  console.log("Connected successfully");
} catch (error) {
  console.error("Connection failed:", error.message);
}
```

### Verify Initialization
After connecting, check the server's capabilities:

```typescript
const serverInfo = client.getServerInfo();
console.log("Server:", serverInfo.name, serverInfo.version);
console.log("Capabilities:", client.getServerCapabilities());
```

## Message Logging

Intercept all messages for debugging:

```typescript
// Log outgoing messages
transport.onMessage = (message) => {
  console.log("← Received:", JSON.stringify(message, null, 2));
};

// Wrap send to log outgoing
const originalSend = transport.send.bind(transport);
transport.send = (message) => {
  console.log("→ Sending:", JSON.stringify(message, null, 2));
  return originalSend(message);
};
```

## Common Client Issues

### Capability Mismatch
The client tries to call a method the server doesn't support.

**Fix:** Check server capabilities before calling methods:
```typescript
const caps = client.getServerCapabilities();
if (caps.tools) {
  const tools = await client.listTools();
}
```

### Request Timeout
Tool calls take too long.

**Fix:** Set appropriate timeouts and use progress tracking:
```typescript
const result = await client.callTool("slow_tool", args, {
  timeout: 60000,
  onProgress: (progress) => console.log(`${progress.progress}/${progress.total}`)
});
```

### Notification Handling
Missing notifications from the server.

**Fix:** Register handlers before connecting:
```typescript
client.setNotificationHandler(
  "notifications/tools/list_changed",
  async () => {
    const { tools } = await client.listTools();
    updateToolList(tools);
  }
);
```

## Health Checks

Implement periodic health checks:

```typescript
setInterval(async () => {
  try {
    await client.ping();
  } catch {
    console.error("Server unresponsive, reconnecting...");
    await reconnect();
  }
}, 30000);
```
