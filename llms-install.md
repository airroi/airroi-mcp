# AI Agent Installation Guide — AirROI MCP Server

This file is for an AI coding agent (Cline, Cursor, Claude Code, etc.) installing the AirROI MCP server on the user's behalf.

## What this server is

AirROI's **remote** MCP server: Airbnb / short-term-rental market analytics (occupancy, ADR, RevPAR, revenue, comparables, ML revenue estimates) for 30,000+ markets, backed by 20M+ tracked listings. It also returns live Airbnb calendars, rates, availability and search rank, and price recommendations.

- Endpoint: `https://mcp.airroi.com`
- Transport: Streamable HTTP
- Auth: `X-API-KEY` request header
- Tools: 29 (all prefixed `airroi_`)

**There is nothing to clone, build, install, or run.** Do not `git clone` this repo, do not `npm install`, do not create a local process. Installation is purely a config-file edit that registers a remote HTTP endpoint.

## Step 1: Get the API key from the user

The server requires an API key. You cannot obtain it yourself — it is tied to the user's account.

Ask the user:

> To connect the AirROI MCP server I need your AirROI API key. Get one free at https://www.airroi.com/api (activate at https://www.airroi.com/api/developer/activate), then paste it here.

Wait for the user to supply the key. Do not proceed with a placeholder. Do not invent, guess, or reuse a key from another source.

## Step 2: Write the configuration

Substitute the user's key for `YOUR_API_KEY`. Keep the header name `X-API-KEY` exactly as written.

### Cline

Edit the Cline MCP settings file (**MCP Servers → Configure MCP Servers**, typically
`~/Library/Application Support/Code/User/globalStorage/saoudrizwan.claude-dev/settings/cline_mcp_settings.json`
on macOS). Merge this entry into the existing `mcpServers` object — do not overwrite servers that are already configured:

```json
{
  "mcpServers": {
    "airroi": {
      "type": "streamableHttp",
      "url": "https://mcp.airroi.com",
      "headers": {
        "X-API-KEY": "YOUR_API_KEY"
      },
      "disabled": false,
      "autoApprove": []
    }
  }
}
```

### Cursor

Merge into `~/.cursor/mcp.json` (user-level) or `.cursor/mcp.json` (project-level):

```json
{
  "mcpServers": {
    "airroi": {
      "url": "https://mcp.airroi.com",
      "headers": {
        "X-API-KEY": "YOUR_API_KEY"
      }
    }
  }
}
```

### Claude Code

Run in the user's shell:

```bash
claude mcp add --transport http airroi https://mcp.airroi.com \
  --header "X-API-KEY: YOUR_API_KEY"
```

### VS Code + Copilot

Merge into `.vscode/mcp.json`. Root key is `"servers"`, not `"mcpServers"`:

```json
{
  "servers": {
    "airroi": {
      "type": "http",
      "url": "https://mcp.airroi.com",
      "headers": {
        "X-API-KEY": "YOUR_API_KEY"
      }
    }
  }
}
```

### Any other client

Transport `Streamable HTTP`, URL `https://mcp.airroi.com`, header `X-API-KEY: <user key>`.

## Step 3: Reload and verify

1. Restart / reload the MCP client so it picks up the new config.
2. Confirm the server connects and lists **29 tools**, all named `airroi_*`.
3. Smoke test with a cheap call, e.g. `airroi_search_markets` with query `"Austin"`, or `airroi_market_summary` for a resolved market. A successful response confirms the key and transport.

## Troubleshooting

| Symptom | Cause / fix |
|---|---|
| `401` / auth error | Key missing, mistyped, or not activated. Re-check the `X-API-KEY` header value; activate at https://www.airroi.com/api/developer/activate |
| Header rejected in Claude Code | The `--header` value must include the name: `"X-API-KEY: abc123"`, not just `"abc123"` |
| Server not listed after edit | Client wasn't reloaded, or the JSON is malformed / the entry was placed under the wrong root key (`servers` vs `mcpServers`) |
| Zero tools discovered | Client is not using Streamable HTTP. Confirm the transport/type field matches the client's expected value |
| Claude Desktop can't connect | Claude Desktop needs the `mcp-remote` bridge (`npx mcp-remote https://mcp.airroi.com --header X-API-KEY:${AIRROI_API_KEY}`, no spaces around the colon); Node.js required |

## Security notes

- The API key is a secret. Write it only into the user's local MCP config; never commit it, echo it into chat logs, or include it in code you generate.
- Prefer user-level config over project-level config so the key does not land in a repository.

## Reference

- Setup guide: https://www.airroi.com/mcp-server/setup
- Tools reference: https://www.airroi.com/mcp-server/tools
- Claude setup: https://www.airroi.com/mcp-server/claude
- Codex setup: https://www.airroi.com/mcp-server/codex
- Registry name: `com.airroi/mcp`
