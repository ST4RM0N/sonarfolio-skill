---
name: sonarfolio
description: "Query and manage Sonarfolio portfolio data via MCP or REST API. Use when user says sonarfolio, portfolio, holdings, assets, positions, LunarCrush portfolio, or wants stock/crypto/ETF tracking data."
type: integration
lifecycle: active
---

# Sonarfolio — Portfolio Layer for NEXUS Prime

Read-only portfolio tracker for stocks, ETFs, and crypto, built by LunarCrush. Aggregates wallets (56 networks), exchanges, and brokerages via Plaid into one portfolio with live market data, onchain metrics, dividends, staking income, and time/money-weighted returns. Exposes the same seven operations over three surfaces: MCP (Streamable HTTP), REST API, and CLI.

## Endpoints

- **MCP:** `https://sonarfolio.com/api/mcp` — Streamable HTTP. Claude/ChatGPT: OAuth sign-in. Other clients: API key as bearer token.
- **REST:** `https://sonarfolio.com/api/v1/` — JSON over HTTPS, bearer token. Example: `curl https://sonarfolio.com/api/v1/assets -H "Authorization: Bearer YOUR_KEY"`
- **CLI:** `npm i -g sonarfolio`, Node 18+. Key via `SONAR_API_KEY` env or `~/.config/sonar/config.json`.

## Seven Tools (all three surfaces mirror these)

| Tool | Method | What it does |
|------|--------|--------------|
| `get_usage` | GET /me | API usage and credit balance |
| `list_held_assets` | GET /assets | Assets currently held |
| `get_asset` | GET /assets/{id} | Single asset details |
| `search_assets` | GET /assets/search | Search across assets |
| `get_portfolio` | GET /portfolio | Portfolio summary: value, allocation, returns |
| `list_holdings` | GET /holdings | Full holdings list |
| `add_position` | POST /holdings | **Only write op** — records a described position. Cannot trade or move funds. |

All tools cover only assets the user holds. Responses include timestamps, disclaimers, and rate-limit headers (`X-RateLimit-Limit`, `X-Credits-Remaining`).

## Rate Limits & Credits

- Free: 100 API credits ($1 per 1,000, never expire)
- Starter ($6/mo): 500 requests/day
- Standard ($10/mo): 1,000/day
- Premium ($16/mo): 2,000/day
- Daily reset at 00:00 UTC. Each MCP tool call, REST call, or CLI command = 1 request.

## NEXUS Prime Integration Notes

- Slot this as the **portfolio layer** on top of the existing LunarCrush API skill (Galaxy Score, AltRank, sentiment, narratives). LunarCrush powers both; Sonarfolio adds holdings, Plaid brokerage sync, wallet aggregation, dividends, and onchain concentration.
- Auth: store the Sonar API key alongside the LunarCrush key. Never commit it.
- The `add_position` write is descriptive only — treat it as a note, not an execution.
- When a number is missing from a response, say so. Do not guess. That is reverse-entropy.

## Anti-Patterns

- Do not confuse Sonarfolio (portfolio tracker) with Sonar (App Store Optimization, trysonar.app) or SonarQube (code quality). Different products, similar names.
- Do not expose the API key in logs, pages, or public repos.
- Do not treat `add_position` as a trade execution.
