# MCP Prompts

Prompts are the third core primitive in MCP. They provide **reusable templates** that structure interactions with language models.

## What Are Prompts?

Prompts are pre-built instruction sets that servers offer to clients. They standardize how models perform common tasks and save developers from repeatedly crafting the same instructions.

## Prompt Definition

Each prompt has:
- **name** — Unique identifier
- **description** — What the prompt does
- **arguments** — Optional parameters to customize the prompt

```json
{
  "name": "code_review",
  "description": "Review code for bugs and improvements",
  "arguments": [
    {
      "name": "language",
      "description": "Programming language",
      "required": true
    },
    {
      "name": "style",
      "description": "Review style: thorough or quick",
      "required": false
    }
  ]
}
```

## Getting a Prompt

```json
{
  "jsonrpc": "2.0",
  "method": "prompts/get",
  "params": {
    "name": "code_review",
    "arguments": {
      "language": "python",
      "style": "thorough"
    }
  },
  "id": 1
}
```

## Prompt Response

Prompts return an array of **messages** ready for the LLM:

```json
{
  "description": "Thorough Python code review",
  "messages": [
    {
      "role": "user",
      "content": {
        "type": "text",
        "text": "Please review the following Python code thoroughly..."
      }
    }
  ]
}
```

## Key Characteristics

- **User-controlled** — Users select which prompt to use
- **Parameterized** — Arguments customize the template
- **Can embed resources** — Prompts can reference resource URIs
- **Discoverable** — Clients list available prompts dynamically
