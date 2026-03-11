# Example: Memory MCP Server

The Memory server provides AI models with **persistent knowledge graph** storage, allowing them to remember information across conversations.

## Concept

The Memory server stores data as a knowledge graph with:
- **Entities** — Named objects (people, projects, concepts)
- **Relations** — Connections between entities
- **Observations** — Facts about entities

## Configuration

```json
{
  "mcpServers": {
    "memory": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-memory"],
      "env": {
        "MEMORY_FILE": "/path/to/memory.json"
      }
    }
  }
}
```

## Tools

| Tool | Description |
|------|-------------|
| `create_entities` | Add new entities to the graph |
| `create_relations` | Create relationships between entities |
| `add_observations` | Add facts about entities |
| `search_nodes` | Search for entities by name or content |
| `read_graph` | Read the entire knowledge graph |
| `delete_entities` | Remove entities from the graph |
| `delete_observations` | Remove specific observations |
| `delete_relations` | Remove relationships |

## Usage Example

After configuration, you can say:

- "Remember that Alice works on Project X as the lead engineer"
- "What do you know about Project X?"
- "Alice has moved to the London office"

The server persists this information to a JSON file on disk.

## Data Structure

```json
{
  "entities": [
    {
      "name": "Alice",
      "entityType": "Person",
      "observations": [
        "Works as lead engineer on Project X",
        "Based in London office"
      ]
    }
  ],
  "relations": [
    {
      "from": "Alice",
      "to": "Project X",
      "relationType": "leads"
    }
  ]
}
```

## Use Cases

- **Personal assistant** memory
- **Project context** persistence
- **User preference** storage
- **Knowledge base** building
