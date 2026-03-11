# Tool Design Best Practices

Well-designed tools are easier for LLMs to use correctly. Follow these guidelines for effective MCP tool design.

## Naming Conventions

**Do:**
- Use clear, verb-first names: `search_files`, `create_issue`, `get_weather`
- Use snake_case for consistency
- Be specific: `search_code` instead of `search`

**Don't:**
- Use vague names: `do_thing`, `process`, `handle`
- Use abbreviations: `srch_fls` instead of `search_files`
- Prefix with the server name: `github_create_issue` → just `create_issue`

## Writing Descriptions

The description is what the LLM reads to decide when to use the tool. Make it:

1. **Concise** — One sentence explaining what the tool does
2. **Specific** — Mention what it operates on
3. **Actionable** — Start with a verb

```
✅ "Search for files matching a glob pattern in the project directory"
❌ "File search tool"
❌ "This tool allows you to search for files by providing a pattern"
```

## Parameter Design

### Use Descriptive Names
```json
{ "city": "string" }        ✅
{ "c": "string" }           ❌
{ "input1": "string" }      ❌
```

### Add Parameter Descriptions
```json
{
  "query": {
    "type": "string",
    "description": "SQL query to execute. Must be a SELECT statement."
  }
}
```

### Minimize Required Parameters
Only require what's truly necessary. Provide sensible defaults for optional parameters.

## Error Messages

Return errors the LLM can understand and act on:

```
✅ "File not found: /path/to/file.txt. Check that the path exists."
❌ "ENOENT: no such file or directory"
```

## Granularity

- Prefer **focused tools** that do one thing well
- Avoid mega-tools with many modes
- Split complex operations into steps the LLM can orchestrate
