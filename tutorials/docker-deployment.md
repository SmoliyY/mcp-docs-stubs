# Deploying MCP Servers with Docker

Docker provides a consistent, reproducible way to deploy MCP servers.

## Dockerfile for TypeScript Server

```dockerfile
FROM node:20-slim

WORKDIR /app

COPY package*.json ./
RUN npm ci --production

COPY dist/ ./dist/

# MCP servers using stdio read from stdin and write to stdout
CMD ["node", "dist/index.js"]
```

## Dockerfile for Python Server

```dockerfile
FROM python:3.12-slim

WORKDIR /app

COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt

COPY . .

CMD ["python", "server.py"]
```

## Running with stdio Transport

For local use with Claude Desktop:

```json
{
  "mcpServers": {
    "my-server": {
      "command": "docker",
      "args": [
        "run", "-i", "--rm",
        "-e", "API_KEY=your-key",
        "my-mcp-server:latest"
      ]
    }
  }
}
```

Key flags:
- `-i` — Keep stdin open (required for stdio transport)
- `--rm` — Remove container after exit
- `-e` — Pass environment variables

## Running with HTTP Transport

```bash
docker run -d \
  -p 3000:3000 \
  -e API_KEY=your-key \
  --name mcp-server \
  my-mcp-server:latest
```

## Docker Compose

```yaml
version: "3.8"
services:
  mcp-server:
    build: .
    ports:
      - "3000:3000"
    environment:
      - DATABASE_URL=postgres://db:5432/mydb
      - API_KEY=${API_KEY}
    depends_on:
      - db

  db:
    image: postgres:16
    environment:
      POSTGRES_DB: mydb
      POSTGRES_PASSWORD: secret
```

## Security

- Don't include secrets in the Docker image
- Use environment variables or Docker secrets
- Run as non-root user
- Use minimal base images
- Scan images for vulnerabilities
