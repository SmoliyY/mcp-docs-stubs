# History of the Model Context Protocol

## Timeline

### November 2024 — Initial Release
Anthropic publicly released MCP as an open-source protocol. The initial specification (version `2024-11-05`) included core primitives: **Tools**, **Resources**, and **Prompts**, along with two transport mechanisms — **stdio** and **HTTP+SSE**.

### Early 2025 — Industry Adoption
Major AI companies adopted MCP. OpenAI integrated MCP support into their Agents SDK. Google DeepMind announced compatibility. The ecosystem grew rapidly with hundreds of community-built MCP servers.

### April 2025 — Security Review
Security researchers published analyses identifying potential vulnerabilities including prompt injection risks and tool permission concerns. This led to significant security improvements in subsequent releases.

### June 2025 — Spec Version `2025-06-18`
Major update introducing:
- **Structured tool output** for richer responses
- **OAuth Resource Server** classification
- **Resource Indicators** (RFC 8707)
- **Elicitation** feature for interactive data collection
- Deprecation of JSON-RPC batching
- Replacement of HTTP+SSE with **Streamable HTTP**

### November 2025 — Spec Version `2025-11-25`
One-year anniversary release with:
- **Tasks** for async operation tracking
- **OpenID Connect Discovery** support
- **Icons metadata** for tools, resources, and prompts
- **Incremental scope consent**
- Enhanced security specifications

## Governance

MCP is now hosted by **The Linux Foundation**, ensuring vendor-neutral governance and open community contributions.
