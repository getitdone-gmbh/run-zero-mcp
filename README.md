# run.zero MCP server

Deploy web apps, APIs and Postgres databases straight from your AI agent. [run.zero](https://run-zero.de/en/) is a platform as a service by Get IT Done GmbH, hosted in Germany on European infrastructure.

- Endpoint: `https://api.run-zero.de/mcp` (Streamable HTTP)
- Sign-in: OAuth 2.1 with PKCE and dynamic client registration. No API key to copy.
- Discovery: `initialize` and `tools/list` answer without sign-in, so you can see every tool first. Tool calls need sign-in.
- Registry name: `de.run-zero/mcp` in the [official MCP Registry](https://registry.modelcontextprotocol.io)

## What your agent can do

| Area | Tools |
| --- | --- |
| Apps | `runzero_create_app`, `runzero_list_apps`, `runzero_get_app`, `runzero_delete_app` |
| Code | `runzero_push_files` (one atomic commit into a managed Git repo), `runzero_list_files`, `runzero_read_file`, `runzero_upload_assets` |
| Deploy | `runzero_deploy`, `runzero_wait_for_build`, `runzero_list_builds`, `runzero_get_build_logs`, `runzero_rebuild_service`, `runzero_deploy_preview` |
| Run | `runzero_get_logs`, `runzero_get_metrics`, `runzero_scale_service`, `runzero_add_service`, `runzero_delete_service` |
| Config | `runzero_set_secrets`, `runzero_list_secrets`, `runzero_delete_secret`, `runzero_describe_addon` |
| Data | `runzero_db_query` against the app's managed Postgres |
| Releases | `runzero_list_releases`, `runzero_restore_release`, `runzero_list_versions`, `runzero_activate_version`, `runzero_publish` |
| Login for your app | `runzero_create_user`, `runzero_list_users`, `runzero_set_user_enabled`, `runzero_create_identity_provider` and more (managed Keycloak realm per app) |
| Help | `runzero_get_help` (workflow, files, addons, procfile, domains) |

The platform builds with buildpacks (Node.js, Python, Go, PHP, Java). The web process listens on `$PORT`. A `run-zero.yaml` declares several services and the addons `postgres` and `keycloak`. Every service gets `https://<service>-<app>.run-zero.de` with TLS, and custom domains get a Let's Encrypt certificate.

## Connect

### Claude Code

```sh
claude mcp add --transport http run-zero https://api.run-zero.de/mcp
```

Then run `/mcp` in Claude Code and sign in.

### Claude Desktop and claude.ai

Settings, Connectors, Add custom connector, URL `https://api.run-zero.de/mcp`.

### Cursor

`~/.cursor/mcp.json`:

```json
{
  "mcpServers": {
    "run-zero": { "url": "https://api.run-zero.de/mcp" }
  }
}
```

### VS Code

```sh
code --add-mcp '{"name":"run-zero","type":"http","url":"https://api.run-zero.de/mcp"}'
```

### Gemini CLI

```sh
gemini extensions install https://github.com/getitdone-gmbh/run-zero-mcp
```

## Try it

Ask your agent:

> Create a run.zero app called hello, push a small Express server that listens on $PORT, deploy it and give me the URL.

## Links

- Website: https://run-zero.de/en/
- MCP page: https://run-zero.de/en/features/mcp
- Pricing: https://run-zero.de/en/pricing
- Dashboard: https://dashboard.run-zero.de
- Contact: info@itdone.de

This repository only describes the hosted server. It does not contain its source code.
