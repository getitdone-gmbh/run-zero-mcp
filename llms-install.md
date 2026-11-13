# Install the run.zero MCP server

run.zero is a hosted remote MCP server. There is nothing to download, build or run locally.

1. Add the server to your MCP client configuration:

```json
{
  "mcpServers": {
    "run-zero": {
      "type": "streamableHttp",
      "url": "https://api.run-zero.de/mcp"
    }
  }
}
```

2. On the first tool call the client opens the browser for the OAuth 2.1 sign-in (PKCE, dynamic client registration). Sign in with your run.zero account and approve access.
3. No API key or environment variable is needed.

To verify the setup, ask the assistant to list the available tools.
