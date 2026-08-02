<div align="center">
  <img src="logo.png" alt="AirROI" width="200" height="200">

  <h1>AirROI MCP Server</h1>

  <p><strong>Official</strong> · Remote · Streamable HTTP · <code>com.airroi/mcp</code></p>
</div>

---

Official AirROI MCP server — Airbnb & short-term rental market analytics: occupancy, ADR, RevPAR, revenue, comparables, and ML revenue estimates for 30,000+ markets worldwide, backed by 20M+ tracked listings. Remote server at https://mcp.airroi.com (Streamable HTTP, X-API-KEY auth, key at airroi.com).

This repository is documentation only. The server is **fully remote** — there is nothing to install, build, or run locally. Point any MCP client at `https://mcp.airroi.com` with an `X-API-KEY` header.

| | |
|---|---|
| **Endpoint** | `https://mcp.airroi.com` |
| **Transport** | Streamable HTTP |
| **Auth** | `X-API-KEY` header |
| **Registry name** | `com.airroi/mcp` |
| **Get an API key** | https://www.airroi.com/api |
| **Tools** | 22 |

## Get an API key

1. Sign up at **https://www.airroi.com/api**
2. Activate the Developer Dashboard at https://www.airroi.com/api/developer/activate
3. Copy your key and use it as the value of the `X-API-KEY` header in the snippets below.

Replace only `YOUR_API_KEY` in the snippets — keep the `X-API-KEY` header name exactly as written.

## Setup

### Claude Code

```bash
claude mcp add --transport http airroi https://mcp.airroi.com \
  --header "X-API-KEY: YOUR_API_KEY"
```

Verify with `/mcp` in a new session, or `claude mcp list`.

### Claude Desktop

Claude Desktop connects via the `mcp-remote` bridge (requires Node.js). Open **Settings → Developer → Edit Config** to edit `claude_desktop_config.json`:

```json
{
  "mcpServers": {
    "airroi": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.airroi.com",
        "--header",
        "X-API-KEY: YOUR_API_KEY"
      ]
    }
  }
}
```

### Cursor

Edit `~/.cursor/mcp.json` (user-level) or `.cursor/mcp.json` (project-level), or use **Settings → Features → MCP → + Add New MCP Server**:

```json
{
  "mcpServers": {
    "airroi": {
      "type": "streamable-http",
      "url": "https://mcp.airroi.com",
      "headers": {
        "X-API-KEY": "YOUR_API_KEY"
      }
    }
  }
}
```

### VS Code (GitHub Copilot)

Requires VS Code 1.99+ and the Copilot extension. Create `.vscode/mcp.json` in your project root, or run **MCP: Open User Configuration** from the Command Palette. Note that VS Code uses `"servers"` as the root key (not `"mcpServers"`):

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

### Cline

Open **MCP Servers → Configure MCP Servers** in Cline and add:

```json
{
  "mcpServers": {
    "airroi": {
      "type": "streamableHttp",
      "url": "https://mcp.airroi.com",
      "headers": {
        "X-API-KEY": "YOUR_API_KEY"
      }
    }
  }
}
```

See [`llms-install.md`](llms-install.md) for agent-driven installation.

### Codex

Codex uses TOML at `~/.codex/config.toml` (user-level) or `.codex/config.toml` (project-level):

```toml
[mcp_servers.airroi]
url = "https://mcp.airroi.com"
http_headers = { "X-API-KEY" = "YOUR_API_KEY" }
```

### Gemini CLI

Edit `~/.gemini/settings.json` (user-level) or `.gemini/settings.json` (project-level). Gemini CLI uses `httpUrl` (not `url`) for HTTP streamable transport:

```json
{
  "mcpServers": {
    "airroi": {
      "httpUrl": "https://mcp.airroi.com",
      "headers": {
        "X-API-KEY": "YOUR_API_KEY"
      }
    }
  }
}
```

### Windsurf

Edit `~/.codeium/windsurf/mcp_config.json` or use **Settings → Plugins**. Windsurf uses `serverUrl` instead of `url`:

```json
{
  "mcpServers": {
    "airroi": {
      "serverUrl": "https://mcp.airroi.com",
      "headers": {
        "X-API-KEY": "YOUR_API_KEY"
      }
    }
  }
}
```

### Any other MCP client

```
Transport: HTTP (Streamable HTTP)
URL: https://mcp.airroi.com
Headers:
  X-API-KEY: YOUR_API_KEY
```

The server is fully remote — no local SDKs or processes required.

## Tools (22)

### Listing tools (9)

| Tool | Description |
|---|---|
| `airroi_get_listing` | Retrieve comprehensive property data including amenities, reviews, pricing, host details, and real-time performance analytics |
| `airroi_batch_listings` | Fetch detailed information for up to 25 properties in a single request |
| `airroi_find_comparables` | Discover up to 25 similar properties ranked by relevance based on location, bedrooms, bathrooms, and guest capacity |
| `airroi_listing_metrics` | Access up to 60 months of historical performance data per listing: monthly occupancy, ADR, RevPAR, and revenue |
| `airroi_listing_future_rates` | View up to 365 days of future nightly rates and availability status for any property |
| `airroi_search_by_market` | Search all active listings within city or neighborhood boundaries using OpenStreetMap market definitions |
| `airroi_search_by_radius` | Find all properties within a 1-100 mile radius from any GPS coordinates |
| `airroi_search_by_polygon` | Search listings within custom geographic boundaries defined by 3+ coordinate points |
| `airroi_export` | Export every listing matching a market, radius, or custom polygon selector to a downloadable file |

### Market tools (10)

| Tool | Description |
|---|---|
| `airroi_market_summary` | Get a quick market overview with key stats: occupancy, ADR, RevPAR, revenue, booking lead time, length of stay, and active listing count |
| `airroi_market_metrics_all` | Access all market metrics combined in one response with monthly time-series and percentile breakdowns |
| `airroi_market_occupancy` | Track historical and seasonal occupancy rate trends with monthly time-series data |
| `airroi_market_adr` | Analyze Average Daily Rate trends with monthly time-series and percentile breakdowns |
| `airroi_market_revpar` | Monitor Revenue Per Available Rental combining occupancy and pricing into a single performance metric |
| `airroi_market_revenue` | Track total market revenue generation with monthly time-series and percentile breakdowns |
| `airroi_market_lead_time` | Understand how far in advance guests book in any market, measured in days |
| `airroi_market_los` | Analyze average guest length-of-stay patterns measured in nights |
| `airroi_market_active_listings` | Monitor market supply growth by tracking active listing counts over time |
| `airroi_market_future_pacing` | Access forward-looking booking pace for upcoming dates with daily pacing data |

### Utility tools (3)

| Tool | Description |
|---|---|
| `airroi_search_markets` | Search for short-term rental markets by name with prefix matching |
| `airroi_lookup_market` | Resolve raw GPS coordinates to the exact market identifiers that every market tool requires |
| `airroi_estimate_revenue` | Generate revenue projections for any location based on bedrooms, bathrooms, and guest capacity |

Full reference: https://www.airroi.com/mcp-server/tools

## Example prompts

Once connected, ask in plain English:

- "What's the average occupancy and ADR for short-term rentals in Austin, Texas over the last 12 months?"
- "Estimate the annual revenue for a 3-bedroom, 2-bath Airbnb sleeping 6 in Joshua Tree, CA."
- "Find 10 comparable listings to Airbnb listing 12345678 and compare their RevPAR."
- "Which neighborhoods in Lisbon have the highest RevPAR, and how has supply grown there over the past two years?"
- "Show me forward booking pace for Scottsdale for the next 90 days — are we ahead of or behind last year?"

## Data coverage

- 20M+ properties tracked
- 30,000+ markets across 190+ countries
- 15+ years of historical data
- Up to 60 months of listing history, 365 days of forward rates

More: https://www.airroi.com/api/data-coverage

## Links

- MCP server overview — https://www.airroi.com/mcp-server
- Setup guide — https://www.airroi.com/mcp-server/setup
- Tools reference — https://www.airroi.com/mcp-server/tools
- Use cases — https://www.airroi.com/mcp-server/use-cases
- REST API — https://www.airroi.com/api
- Contact — https://www.airroi.com/contact

## License

MIT — see [LICENSE](LICENSE).
