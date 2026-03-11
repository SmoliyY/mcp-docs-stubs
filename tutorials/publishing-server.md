# Publishing an MCP Server

Once your MCP server is ready, you can publish it for others to use. This guide covers publishing to npm and PyPI.

## Publishing to npm (TypeScript)

### 1. Prepare package.json

```json
{
  "name": "mcp-server-myservice",
  "version": "1.0.0",
  "description": "MCP server for MyService integration",
  "bin": {
    "mcp-server-myservice": "./dist/index.js"
  },
  "files": ["dist"],
  "keywords": ["mcp", "model-context-protocol", "ai"],
  "license": "MIT"
}
```

### 2. Add Shebang

Add to the top of your entry file:
```typescript
#!/usr/bin/env node
```

### 3. Build and Publish

```bash
npm run build
npm publish
```

### 4. Users Install With

```json
{
  "mcpServers": {
    "myservice": {
      "command": "npx",
      "args": ["-y", "mcp-server-myservice"]
    }
  }
}
```

## Publishing to PyPI (Python)

### 1. Create pyproject.toml

```toml
[project]
name = "mcp-server-myservice"
version = "1.0.0"
description = "MCP server for MyService"
requires-python = ">=3.10"
dependencies = ["mcp>=1.0.0"]

[project.scripts]
mcp-server-myservice = "mcp_server_myservice:main"
```

### 2. Build and Publish

```bash
pip install build twine
python -m build
twine upload dist/*
```

## Naming Conventions

- Prefix with `mcp-server-` for discoverability
- Use lowercase with hyphens: `mcp-server-my-service`
- Be descriptive but concise

## Documentation Checklist

- [ ] README with description and features
- [ ] Installation instructions
- [ ] Configuration examples for popular hosts
- [ ] Required environment variables
- [ ] Available tools, resources, and prompts
- [ ] Security considerations
- [ ] License
