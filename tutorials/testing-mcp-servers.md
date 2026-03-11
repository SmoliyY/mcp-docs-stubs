# Testing MCP Servers

Thorough testing ensures your MCP server works reliably across different clients. This guide covers testing strategies.

## Unit Testing Tools

Test your tool logic independently from the MCP protocol layer.

### TypeScript with Jest

```typescript
import { describe, test, expect } from "@jest/globals";
import { searchFiles } from "./tools/search";

describe("searchFiles", () => {
  test("returns matching files", async () => {
    const result = await searchFiles({ query: "*.ts", path: "./src" });
    expect(result).toContain("index.ts");
  });

  test("returns empty for no matches", async () => {
    const result = await searchFiles({ query: "*.xyz", path: "./src" });
    expect(result).toHaveLength(0);
  });
});
```

### Python with pytest

```python
import pytest
from tools.search import search_files

def test_search_returns_matches():
    result = search_files(query="*.py", path="./src")
    assert len(result) > 0

def test_search_no_matches():
    result = search_files(query="*.xyz", path="./src")
    assert result == []
```

## Integration Testing

Test the full MCP server with a client:

```typescript
import { Client } from "@modelcontextprotocol/sdk/client/index.js";
import { StdioClientTransport } from "@modelcontextprotocol/sdk/client/stdio.js";

test("server lists tools correctly", async () => {
  const client = new Client({ name: "test", version: "1.0.0" });
  const transport = new StdioClientTransport({
    command: "node", args: ["dist/index.js"]
  });

  await client.connect(transport);
  const { tools } = await client.listTools();

  expect(tools).toHaveLength(3);
  expect(tools[0].name).toBe("search_files");

  await client.close();
});
```

## Testing with MCP Inspector

The Inspector is ideal for manual testing:

```bash
npx @modelcontextprotocol/inspector node dist/index.js
```

Verify:
- All tools appear with correct schemas
- Tool calls return expected results
- Error cases are handled properly
- Resources are readable
- Prompts generate valid messages

## CI/CD Testing

Add MCP tests to your CI pipeline to catch regressions automatically.
