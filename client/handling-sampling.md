# Handling Sampling Requests

When a client declares sampling support, servers can request LLM completions. The client must handle these requests appropriately.

## Declaring Support

```json
{
  "capabilities": {
    "sampling": {}
  }
}
```

## Handling the Request

When a server sends `sampling/createMessage`:

```typescript
client.setRequestHandler("sampling/createMessage", async (request) => {
  const { messages, maxTokens, temperature, systemPrompt } = request.params;

  // 1. Show to user for approval (human-in-the-loop)
  const approved = await showSamplingApproval(messages);
  if (!approved) {
    throw new Error("User declined sampling request");
  }

  // 2. Forward to your LLM
  const response = await callLLM({
    messages,
    maxTokens,
    temperature,
    system: systemPrompt
  });

  // 3. Return the result
  return {
    role: "assistant",
    content: {
      type: "text",
      text: response.text
    },
    model: "claude-sonnet-4-5-20250929",
    stopReason: "endTurn"
  };
});
```

## Security Considerations

### Human-in-the-Loop
Always show sampling requests to the user before processing. The user should be able to:
- **Review** the messages being sent to the LLM
- **Modify** the content if needed
- **Reject** the request entirely

### Content Filtering
- Sanitize messages for potential prompt injection
- Apply your standard content policies
- Log sampling requests for auditing

### Rate Limiting
- Limit how often servers can request sampling
- Prevent runaway sampling loops
- Set maximum token limits

## Best Practices

- Show clear UI when sampling is requested
- Let users opt-in to auto-approve for trusted servers
- Include the server name in approval dialogs
- Log all sampling activity
