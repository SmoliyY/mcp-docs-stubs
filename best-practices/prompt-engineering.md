# MCP Prompt Engineering Best Practices

Writing effective tool descriptions and prompt templates is crucial for reliable MCP integrations.

## Tool Descriptions

The tool description is the primary way an LLM decides when and how to use a tool.

### Be Specific About When to Use

```
✅ "Search for files by name pattern using glob syntax (e.g., '*.ts', 'src/**/*.js')"
❌ "Search files"
```

### Describe the Output

```
✅ "Returns a JSON array of matching file paths with their sizes"
❌ "Returns results"
```

### Include Examples

```
✅ "Execute a SQL SELECT query against the database. Example: 'SELECT name FROM users WHERE age > 30'"
❌ "Run a query"
```

### State Limitations

```
✅ "Read a file up to 10MB. For larger files, use read_file_chunk with offset and limit parameters."
❌ "Read a file"
```

## Parameter Descriptions

### Specify Format

```json
{
  "date": {
    "type": "string",
    "description": "Date in ISO 8601 format (YYYY-MM-DD)"
  }
}
```

### Include Valid Ranges

```json
{
  "limit": {
    "type": "integer",
    "description": "Number of results to return (1-100, default: 10)"
  }
}
```

## Prompt Templates

### Be Instructive
```
✅ "You are reviewing {language} code. Focus on: 1) Security vulnerabilities, 2) Performance issues, 3) Code style. Rate each issue as critical, warning, or info."
```

### Provide Structure
```
✅ "Analyze the error and respond with: 1) Root cause, 2) Impact, 3) Suggested fix with code example"
```

### Set Boundaries
```
✅ "Only suggest changes within the scope of the provided file. Do not suggest architectural changes."
```

## Testing Your Descriptions

- Ask different LLMs to explain when they'd use each tool
- Test with ambiguous queries to see if the right tool is selected
- Check if the LLM provides correct parameter formats
