# Example: Puppeteer MCP Server

The Puppeteer MCP server enables AI models to control a web browser — navigating pages, taking screenshots, filling forms, and extracting content.

## Configuration

```json
{
  "mcpServers": {
    "puppeteer": {
      "command": "npx",
      "args": ["-y", "@modelcontextprotocol/server-puppeteer"]
    }
  }
}
```

## Tools

| Tool | Description |
|------|-------------|
| `puppeteer_navigate` | Navigate to a URL |
| `puppeteer_screenshot` | Take a screenshot of the page |
| `puppeteer_click` | Click an element by CSS selector |
| `puppeteer_type` | Type text into an input field |
| `puppeteer_evaluate` | Execute JavaScript on the page |
| `puppeteer_get_content` | Get the page's text content |

## Resources

The server exposes the current page as a resource:

```
browser://screenshot    → Current page screenshot (image)
browser://console       → Browser console logs (text)
```

## Example Interactions

With this server configured, you can ask the AI to:

- "Navigate to example.com and take a screenshot"
- "Fill in the login form with username 'test' and click submit"
- "Extract all the product names from this e-commerce page"
- "Check if the sign-up button is visible on the page"

## How It Works

1. Server launches a headless Chromium browser
2. The AI calls tools to control the browser
3. Screenshots are returned as base64-encoded images
4. Page content is extracted and returned as text

## Use Cases

- **Web testing** — Automated UI verification
- **Data extraction** — Scraping structured data
- **Form automation** — Filling and submitting forms
- **Visual verification** — Checking page layouts
- **Monitoring** — Checking website availability

## Notes

- The browser runs headless by default
- Some sites may block automated browsers
- Resource-intensive — each session uses memory
