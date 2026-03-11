# Resource Design Best Practices

Well-designed resources make data easily accessible to AI applications. Follow these principles.

## URI Scheme Design

Use clear, hierarchical URI schemes:

```
✅ file:///home/user/project/src/index.ts
✅ db://mydb/users/schema
✅ github://owner/repo/issues/123

❌ resource://1234
❌ data://blob
```

## Naming Resources

- Use human-readable names
- Include the data type in the description
- Specify the MIME type accurately

```json
{
  "uri": "db://mydb/users/schema",
  "name": "Users Table Schema",
  "description": "Database schema for the users table including all columns and constraints",
  "mimeType": "application/json"
}
```

## Templates vs Static Resources

### Use Static Resources When:
- The data set is small and known
- URIs don't change frequently
- Every resource should be discoverable

### Use Templates When:
- Resources are parameterized (user IDs, file paths)
- The set of possible resources is large or infinite
- Resources follow a predictable pattern

## Content Format

- Return **JSON** for structured data
- Return **Markdown** for documentation
- Return **plain text** for logs and simple content
- Use **Base64** only for true binary data (images, PDFs)

## Performance

- Keep resource reads **fast** (< 1 second)
- Cache expensive reads
- Limit response sizes (no multi-megabyte responses)
- Use subscriptions for frequently changing data instead of polling

## Subscriptions

Implement subscriptions for resources that change:
- File contents
- Database records
- Configuration values
- Live metrics

This avoids unnecessary polling and keeps the LLM context current.
