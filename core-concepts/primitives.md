# MCP Primitives

MCP defines three core **primitives** that servers can expose to clients. Each primitive serves a different purpose in the AI integration workflow.

## Overview

| Primitive | Control | Direction | Analogy |
|-----------|---------|-----------|---------|
| **Tools** | Model-controlled | Client → Server | POST endpoint |
| **Resources** | Application-controlled | Client → Server | GET endpoint |
| **Prompts** | User-controlled | Client → Server | Template |

## Tools

Tools are **executable functions** that the AI model can decide to invoke. The model reads the tool description and decides when and how to use it.

- Controlled by the **model** (LLM decides when to call)
- Can have **side effects** (write data, send messages)
- Accept **input parameters** defined by a JSON Schema
- Return **structured or text content**

## Resources

Resources are **data sources** that provide contextual information. The application (not the model) typically decides when to fetch resources.

- Controlled by the **application**
- Should be **read-only** (no side effects)
- Identified by **URIs** (e.g., `file:///path/to/doc.md`)
- Can be **subscribed to** for change notifications

## Prompts

Prompts are **reusable templates** that structure interactions. Users typically select which prompt to use.

- Controlled by the **user**
- Provide **pre-built instructions** for common tasks
- Accept **arguments** to customize the template
- Return **message arrays** ready for the LLM

## Choosing the Right Primitive

- Need the model to **perform an action**? → Use a **Tool**
- Need to **provide data** to the model? → Use a **Resource**
- Need to **guide the model's behavior**? → Use a **Prompt**
