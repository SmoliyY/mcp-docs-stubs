# Resource Subscriptions

MCP supports **subscribing** to resource changes, allowing clients to receive real-time updates when resource content changes.

## Prerequisites

The server must declare subscription support during initialization:

```json
{
  "capabilities": {
    "resources": {
      "subscribe": true
    }
  }
}
```

## Subscribing

```json
{
  "jsonrpc": "2.0",
  "method": "resources/subscribe",
  "params": {
    "uri": "file:///config/settings.json"
  },
  "id": 1
}
```

## Receiving Updates

When a subscribed resource changes, the server sends a notification:

```json
{
  "jsonrpc": "2.0",
  "method": "notifications/resources/updated",
  "params": {
    "uri": "file:///config/settings.json"
  }
}
```

The client should then re-read the resource to get the updated content.

## Unsubscribing

```json
{
  "jsonrpc": "2.0",
  "method": "resources/unsubscribe",
  "params": {
    "uri": "file:///config/settings.json"
  },
  "id": 2
}
```

## List Change Notifications

Separate from individual resource updates, the server can notify when the overall resource list changes:

```json
{
  "jsonrpc": "2.0",
  "method": "notifications/resources/list_changed"
}
```

This indicates new resources were added or existing ones removed.

## Use Cases

- **File watchers** — Notify when source files change
- **Database monitors** — Alert on record updates
- **Config reloading** — Track configuration file changes
- **Live dashboards** — Stream metric updates
