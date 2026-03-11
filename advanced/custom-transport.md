# Custom Transports

MCP allows custom transport implementations beyond stdio and Streamable HTTP. Any mechanism that carries JSON-RPC messages bidirectionally can serve as a transport.

## Transport Interface

A transport must implement:

```typescript
interface Transport {
  // Send a JSON-RPC message
  send(message: JSONRPCMessage): Promise<void>;

  // Called when a message is received
  onMessage?: (message: JSONRPCMessage) => void;

  // Called when the connection closes
  onClose?: () => void;

  // Called on errors
  onError?: (error: Error) => void;

  // Start the transport
  start(): Promise<void>;

  // Close the transport
  close(): Promise<void>;
}
```

## WebSocket Transport

```typescript
class WebSocketTransport implements Transport {
  private ws: WebSocket;

  constructor(url: string) {
    this.ws = new WebSocket(url);
  }

  async start() {
    return new Promise<void>((resolve) => {
      this.ws.onopen = () => resolve();
      this.ws.onmessage = (event) => {
        const message = JSON.parse(event.data);
        this.onMessage?.(message);
      };
      this.ws.onclose = () => this.onClose?.();
      this.ws.onerror = (e) => this.onError?.(e);
    });
  }

  async send(message: JSONRPCMessage) {
    this.ws.send(JSON.stringify(message));
  }

  async close() {
    this.ws.close();
  }
}
```

## Unix Domain Socket Transport

Useful for high-performance local communication:

```typescript
class UnixSocketTransport implements Transport {
  constructor(private socketPath: string) {}

  async start() {
    this.socket = net.connect(this.socketPath);
    // Handle line-delimited JSON messages
  }
}
```

## When to Use Custom Transports

- **WebSocket** — When you need persistent bidirectional streaming
- **Unix sockets** — For high-performance local IPC
- **Named pipes** — Windows-specific local communication
- **gRPC** — When integrating with gRPC-based systems

## Considerations

- Ensure reliable, ordered message delivery
- Handle reconnection logic
- Implement proper error propagation
- Support graceful shutdown
