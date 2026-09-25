<div align="center">
  <img src="logo.png" alt="AirROI" width="160" height="160">

  <h1>AirROI Airbnb MCP Server</h1>

  <p><strong>Short-term rental market data for AI assistants</strong> · Remote · Streamable HTTP · <code>com.airroi/mcp</code></p>
</div>

---

AirROI's Airbnb MCP server gives Claude, Codex, Cursor, VS Code and any other Model Context Protocol client **29 tools** for Airbnb and short-term rental market data: occupancy, ADR, RevPAR, revenue percentiles, comparables, revenue estimates, live Airbnb calendars and search rank, and price recommendations. It covers **20M+ listings in 190+ countries** and answers from a maintained dataset, so your assistant never scrapes airbnb.com.

This repository is documentation only. The server is fully remote at `https://mcp.airroi.com`; there is nothing to install or run. AirROI is an independent data provider and is not affiliated with Airbnb. As of September 2026, Airbnb has no official MCP server or public API.

| | |
|---|---|
| **Endpoint** | `https://mcp.airroi.com` |
| **Transport** | Streamable HTTP (remote) |
| **Auth** | `X-API-KEY` header, required on every request |
| **Tools** | 29 (11 market, 13 listing, 3 lookup and estimate, 2 pricing) |
| **Price** | From $0.01 per call, pay as you go |
| **Registry** | [`com.airroi/mcp`](https://registry.modelcontextprotocol.io/v0/servers?search=com.airroi/mcp) in the official MCP Registry |
| **Docs** | https://www.airroi.com/mcp-server |

## Which Airbnb MCP server do you need?

"Airbnb MCP server" can mean three different things. Pick by the job:

| You want to… | Server type | Examples | AirROI? |
|---|---|---|---|
| Find a place to stay | Listing-search scraper | openbnb `mcp-server-airbnb`, Apify, Bright Data | No |
| Run your own listings (messages, reservations, calendars, prices) | Property-management or pricing server | Hospitable, Guesty, PriceLabs, Wheelhouse | No |
| Analyze a market, a property or a comp set | Market-data MCP | **AirROI** | **Yes** |

A side-by-side comparison with pricing and limitations: https://www.airroi.com/mcp-server/compare

## Get an API key

1. Sign up for a free API key at https://www.airroi.com/api
2. Activate it in the developer dashboard at https://www.airroi.com/api/developer/activate and copy the key.
3. Use it as the `X-API-KEY` header value in the snippets below. Replace only `YOUR_API_KEY`.

Discovering tools (`tools/list`) is not billed, but every request needs the key.

## Setup

Full, per-client guides: [Claude](https://www.airroi.com/mcp-server/claude) · [Codex and ChatGPT](https://www.airroi.com/mcp-server/codex) · [every other client](https://www.airroi.com/mcp-server/setup)

### Claude Code

```bash
claude mcp add --transport http airroi \
  https://mcp.airroi.com \
  --header "X-API-KEY: YOUR_API_KEY" --scope user
```

Check it with `claude mcp get airroi`, then ask Claude a market question.

### Claude Desktop

Claude Desktop connects to remote servers with custom headers through the `mcp-remote` bridge (needs Node.js). Edit `~/Library/Application Support/Claude/claude_desktop_config.json` (macOS) or `%APPDATA%\Claude\claude_desktop_config.json` (Windows), then restart the app:

```json
{
  "mcpServers": {
    "airroi": {
      "command": "npx",
      "args": [
        "mcp-remote",
        "https://mcp.airroi.com",
        "--header",
        "X-API-KEY:${AIRROI_API_KEY}"
      ],
      "env": { "AIRROI_API_KEY": "YOUR_API_KEY" }
    }
  }
}
```

Keep `X-API-KEY:${AIRROI_API_KEY}` free of spaces around the colon.

### Codex (CLI, IDE extension and ChatGPT desktop app)

All three read `~/.codex/config.toml`:

```toml
[mcp_servers.airroi]
url = "https://mcp.airroi.com"
env_http_headers = { "X-API-KEY" = "AIRROI_API_KEY" }
```

Then `export AIRROI_API_KEY=YOUR_API_KEY` in your shell profile. `codex mcp list` shows `Auth: Unsupported` for header auth; that is expected. ChatGPT on the web (developer mode) doesn't support API-key servers, so use Codex.

### Cursor

`~/.cursor/mcp.json` (global) or `.cursor/mcp.json` (project):

```json
{
  "mcpServers": {
    "airroi": {
      "url": "https://mcp.airroi.com",
      "headers": { "X-API-KEY": "${env:AIRROI_API_KEY}" }
    }
  }
}
```

### VS Code (Copilot agent mode)

`.vscode/mcp.json`. The top-level key is `servers`, not `mcpServers`:

```json
{
  "inputs": [
    {
      "type": "promptString",
      "id": "airroi-key",
      "description": "AirROI API key",
      "password": true
    }
  ],
  "servers": {
    "airroi": {
      "type": "http",
      "url": "https://mcp.airroi.com",
      "headers": { "X-API-KEY": "${input:airroi-key}" }
    }
  }
}
```

### Devin Desktop (formerly Windsurf)

`~/.config/devin/mcp_config.json` (Windows: `%APPDATA%\devin\mcp_config.json`):

```json
{
  "mcpServers": {
    "airroi": {
      "serverUrl": "https://mcp.airroi.com",
      "headers": { "X-API-KEY": "${env:AIRROI_API_KEY}" }
    }
  }
}
```

### Antigravity CLI (Gemini CLI's successor)

`~/.gemini/config/mcp_config.json` (global) or `.agents/mcp_config.json` (project):

```json
{
  "mcpServers": {
    "airroi": {
      "serverUrl": "https://mcp.airroi.com",
      "headers": { "X-API-KEY": "YOUR_API_KEY" }
    }
  }
}
```

### n8n

1. Add an AI Agent node, then attach an MCP Client Tool node to it.
2. Set the endpoint to `https://mcp.airroi.com` and the transport to HTTP Streamable.
3. Choose Header Auth and create a credential with name `X-API-KEY` and your key as the value.

### OpenAI Agents SDK (Python)

```python
import asyncio, os
from agents import Agent, Runner
from agents.mcp import MCPServerStreamableHttp

async def main():
    async with MCPServerStreamableHttp(
        name="airroi",
        params={
            "url": "https://mcp.airroi.com",
            "headers": {"X-API-KEY": os.environ["AIRROI_API_KEY"]},
        },
    ) as airroi:
        agent = Agent(name="STR analyst", mcp_servers=[airroi])
        result = await Runner.run(
            agent, "Occupancy and ADR for 2-bedroom homes in Nashville?"
        )
        print(result.final_output)

asyncio.run(main())
```

### Any stdio-only client

```bash
npx mcp-remote https://mcp.airroi.com --header X-API-KEY:${AIRROI_API_KEY}
```

## A real session

Prompt, run on 2026-09-25 against `mcp.airroi.com`:

> Compare Austin and Nashville for a 2-bedroom entire-home Airbnb: occupancy, ADR, RevPAR, annual revenue, and how many competing listings I'd face.

Tool calls: `airroi_search_markets` → `airroi_search_markets` → `airroi_market_summary` → `airroi_market_summary` → `airroi_market_revenue` → `airroi_market_revenue` (cost $0.42)

> Over the trailing 12 months, 2-bedroom entire homes in Nashville earned more per listing: $40,075 in average annual revenue versus $24,965 in Austin. Occupancy is nearly identical (49% vs 48%), so the gap comes from rate: Nashville's ADR is $296 against Austin's $236, for RevPAR of $146 versus $114. Nashville also has less 2-bedroom supply, about 1,270 active listings a month versus about 2,029 in Austin. October 2025 was the strongest month for the median listing in both markets, at $5,009 in Nashville and $3,941 in Austin.

More sessions (live search rank, owner reports, forward pacing, Lisbon): https://www.airroi.com/mcp-server/use-cases

## Tools (29)

No tool can change a listing, price, booking or message. 26 tools are flagged read-only; the other 3 create a download file or a scan cursor.

### Market analytics (11)

| Tool | What it does | Price per call |
|---|---|---|
| `airroi_market_min_nights` | Track monthly average minimum-night settings and percentile breakdowns across a market. Use this to understand local stay restrictions alongside occupancy and pricing trends. | $0.10 |
| `airroi_market_summary` | Get a quick market overview with key stats: occupancy, ADR, RevPAR, average annual revenue per listing, booking lead time, length of stay, minimum nights, and active listing count. Returns a single-response trailing-12-month snapshot of any market. | $0.10 |
| `airroi_market_metrics_all` | Access all market metrics combined in one response. Returns monthly time-series with percentile breakdowns (avg, p25, p50, p75, p90) for occupancy, ADR, RevPAR, revenue, lead time, length of stay, minimum nights, and active listings -- the most comprehensive single tool. | $0.50 |
| `airroi_market_occupancy` | Track historical and seasonal occupancy rate trends with monthly time-series data. Returns average and percentile breakdowns to identify peak demand periods and booking patterns. | $0.10 |
| `airroi_market_adr` | Analyze Average Daily Rate trends with monthly time-series and percentile breakdowns. Track pricing compression, seasonal fluctuations, and rate growth across an entire market. | $0.10 |
| `airroi_market_revpar` | Monitor Revenue Per Available Rental combining occupancy and pricing into a single performance metric. Returns monthly time-series with percentile distributions for benchmarking. | $0.10 |
| `airroi_market_revenue` | Track monthly listing revenue with time-series and percentile breakdowns. Analyze revenue growth trends and seasonal patterns for investment opportunity sizing. | $0.10 |
| `airroi_market_lead_time` | Understand how far in advance guests book in any market, measured in days. Returns monthly time-series with percentiles to optimize pricing and marketing timing strategies. | $0.10 |
| `airroi_market_los` | Analyze average guest length-of-stay patterns measured in nights. Returns monthly time-series with percentile breakdowns for operational planning and minimum-stay optimization. | $0.10 |
| `airroi_market_active_listings` | Monitor market supply growth by tracking active listing counts over time. Identify new entrants, market saturation, and competitive landscape changes with monthly time-series data. | $0.10 |
| `airroi_market_future_pacing` | Access forward-looking booking pace for upcoming dates. Returns daily pacing data with booked count, available count, average rates, and fill rate for demand forecasting and revenue prediction. | $0.20 |

### Market lookup and revenue estimates (3)

| Tool | What it does | Price per call |
|---|---|---|
| `airroi_search_markets` | Search for short-term rental markets by name with prefix matching. Returns the exact market identifiers (country, region, locality, district) with active listing counts needed by all market tools. | $0.01 |
| `airroi_lookup_market` | Resolve raw GPS coordinates to the exact market identifiers (country, region, locality, district) that every market tool requires. The reverse of search_markets — use it when you have a latitude/longitude instead of a place name. | $0.01 |
| `airroi_estimate_revenue` | Generate revenue projections for any location based on bedrooms, bathrooms, and guest capacity. Returns projected annual revenue, ADR, occupancy with percentile breakdowns (p25-p90), monthly distribution, and comparable listings. | $0.20 |

### Listing data (4)

| Tool | What it does | Price per call |
|---|---|---|
| `airroi_get_listing` | Retrieve full data for one Airbnb listing by ID: property details, description text, photo URLs, check-in and check-out times, guest-favorite status, pricing, ratings, and performance metrics including trailing-12-month and last-90-day occupancy, ADR, and revenue. | $0.10 |
| `airroi_batch_listings` | Fetch detailed information for up to 25 properties in a single billed request. Returns counts and a downloadable JSON resource containing full property details and any per-ID errors. Downloading the resource does not repeat the API call. | $1.00 |
| `airroi_find_comparables` | Discover up to 25 similar properties ranked by relevance based on location, bedrooms, bathrooms, and guest capacity. Specify location via coordinates or a physical address, and optionally a 1–10 mile radius and room type, for competitive benchmarking. | $0.10 |
| `airroi_listing_metrics` | Access up to 60 months of historical performance data per listing: monthly occupancy, ADR, RevPAR, revenue, and average minimum-night setting. Ideal for trend analysis, seasonality detection, and investment underwriting. | $0.10 |

### Listing search and export (4)

| Tool | What it does | Price per call |
|---|---|---|
| `airroi_search_by_market` | Search active listings within a city, region, or neighborhood using OpenStreetMap market definitions, or globally when no market is given. Filter by property attributes, host details, pricing, ratings, and performance metrics; returns up to 10 compact summaries per call. | $0.50 |
| `airroi_search_by_radius` | Find properties within a 1-100 mile radius of any GPS coordinates. Supports the same filtering and sorting as market search for location-based competitive analysis; returns up to 10 compact summaries per call. | $0.50 |
| `airroi_search_by_polygon` | Search listings within custom geographic boundaries defined by 3+ coordinate points. Draw any shape to define your search area for precise market segmentation and neighborhood analysis; returns up to 10 compact summaries per call. | $0.50 |
| `airroi_export` | Export every listing matching a market, radius, or custom polygon selector to a single downloadable file, delivered as a presigned download URL valid for 7 days. The full dataset is never streamed into the conversation — you get metadata, a short preview, and the download link to save locally. Choose JSONL (one full nested listing object per line) or CSV (flattened summary columns). Pagination is handled internally up to a 100,000-listing cap; very large exports return a partial file with counts if the time budget is reached. Ideal for bulk analysis, data pipelines, and offline scripting. | $0.50 (1 call per 10 listings) |

### Live Airbnb data (5)

| Tool | What it does | Price per call |
|---|---|---|
| `airroi_live_calendar` | Fetch a listing's calendar live from Airbnb: nightly rates (excluding the cleaning fee), availability, and minimum stays for the next 12 months, plus the host's cleaning fee and short-stay cleaning fee. Booked and host-blocked nights both show as unavailable. | $0.20 |
| `airroi_live_rates` | Fetch just a listing's nightly rates live for the next 12 months, in native currency or US dollars. Use it for competitor price tracking when availability is not needed. | $0.10 |
| `airroi_live_availability` | Fetch a listing's availability and stay rules live for the next 12 months: open or unavailable nights, minimum and maximum stay, and whether guests can check in or check out on each date. | $0.10 |
| `airroi_live_search_ranking` | See the listings Airbnb search shows for a map rectangle, in rank order (up to 270). Use it to check where a listing appears in search against its competitors. | $0.20 |
| `airroi_live_scan` | Find every Airbnb listing ID inside a custom polygon, live, up to 100 IDs per page. Continue with the short scan_id it returns; the server keeps the scan's progress, so each page stays small. Pair it with get_listing or batch_listings for full details. | $0.50 |

### Price recommendations (2)

| Tool | What it does | Price per call |
|---|---|---|
| `airroi_recommend_base_price` | Estimate a year-round nightly starting price from property coordinates and known property facts. Optional amenities, reviews, and other details can refine the estimate. Nothing is saved or published. | $0.10 |
| `airroi_recommend_calendar_prices` | Calculate nightly prices from coordinates, currency, and a base price. Modeled seasonality, weekdays, events, and demand combine with optional pricing rules, seasonal overrides, price limits, and stay restrictions. | $0.10 |

Full reference with parameters: https://www.airroi.com/mcp-server/tools

## What a question costs

A typical market analysis takes six calls:

| Call | Price |
|---|---|
| `airroi_search_markets` | $0.01 |
| `airroi_market_summary` | $0.10 |
| `airroi_market_occupancy` | $0.10 |
| `airroi_market_adr` | $0.10 |
| `airroi_market_revenue` | $0.10 |
| `airroi_market_future_pacing` | $0.20 |
| **Total** | **$0.61** |

Price list: https://www.airroi.com/api/pricing

## Data coverage

- 20M+ listings in 190+ countries and 30,000+ markets
- Monthly occupancy, ADR, RevPAR and revenue with p25/p50/p75/p90 percentiles, up to 60 months per request
- Live Airbnb calendars, rates, availability and search rank, up to 12 months ahead
- Native currencies, or USD on request

## What's new

- **2026-09-24** · Short-stay cleaning fees: Live calendar and listing tools return the host's cleaning fee for 1–2 night stays alongside the standard cleaning fee.
- **2026-09-23** · Cleaning fees on the live calendar: airroi_live_calendar returns the cleaning fee set by the host; nightly rates exclude it.
- **2026-09-22** · Five live Airbnb tools: Live calendar, rates, availability, search ranking and polygon scan replaced the future-rates tool.
- **2026-09-08** · Price recommendations: airroi_recommend_base_price and airroi_recommend_calendar_prices expose the pricing engine through MCP.

## Links

- Airbnb MCP server overview: https://www.airroi.com/mcp-server
- Connect Claude to Airbnb data: https://www.airroi.com/mcp-server/claude
- Connect Codex and ChatGPT to Airbnb data: https://www.airroi.com/mcp-server/codex
- Best Airbnb MCP servers compared: https://www.airroi.com/mcp-server/compare
- Setup for every client: https://www.airroi.com/mcp-server/setup
- Tool reference: https://www.airroi.com/mcp-server/tools
- Use cases: https://www.airroi.com/mcp-server/use-cases
- REST API (same data, same key): https://www.airroi.com/api

## License

MIT, see [LICENSE](LICENSE).
