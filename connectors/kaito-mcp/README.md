# Kaito MCP Connector

Give Claude direct access to Kaito's crypto market intelligence — sentiment, mindshare, narrative tracking, KOL signals, and social intelligence, all queryable in plain language.

Kaito aggregates and ranks crypto Twitter/X content using a smart engagement model that weights signals from influential accounts. The MCP server exposes 15 tools covering everything from token sentiment time series to narrative lifecycle tracking to who the smartest money is following right now.

This is the primary data source used by [V3 Daily KOL](../../plugins/v3-daily-kol/) for topic discovery and narrative momentum detection.

Source: [MetaSearch-IO/kaito-mcp-server](https://github.com/MetaSearch-IO/kaito-mcp-server)

---

## What you can do

```
What's the sentiment trend for $SOL over the last 30 days?
```
```
Which narratives have the most mindshare growth this week?
```
```
Who are the top KOLs talking about Morpho right now?
```
```
What tokens are the smartest accounts in crypto following?
```
```
Find me the highest-signal posts about RWA from the last 7 days
```
```
What catalyst events are coming up for $ETH?
```

---

## Available Tools

### Market Intelligence

| Tool | What it does |
|------|-------------|
| `kaito_sentiment` | Daily sentiment time series for a token — volume-weighted bullish/bearish scores with notable events |
| `kaito_mindshare` | Daily mindshare for a token — its share of total crypto Twitter conversation |
| `kaito_narrative_mindshare` | Mindshare for a narrative (AI, DeFi, L2, RWA, etc.) |
| `kaito_mentions` | Daily mention counts broken down by source (Twitter, Discord, News) |
| `kaito_engagement` | Total and smart/KOL engagement metrics over time |

### Search

| Tool | What it does |
|------|-------------|
| `kaito_advanced_search` | Ranked crypto feeds with AI summaries, sentiment scores, and filters for likes, views, retweets, author type, language, and more |

### Social Intelligence

| Tool | What it does |
|------|-------------|
| `kaito_smart_followers` | Cumulative smart follower count or list of smart followers gained on a specific date |
| `kaito_smart_following` | Latest 100 smart accounts a user follows, in reverse chronological order |
| `kaito_get_twitter_user` | Twitter profile data for any handle |
| `kaito_kol_token_mindshare` | Top KOLs by mindshare for a specific token |
| `kaito_market_smart_following` | Smart follower activity across the market |

### Rankings

| Tool | What it does |
|------|-------------|
| `kaito_mindshare_arena` | Project rankings by mindshare with category and time window filters |
| `kaito_mindshare_delta` | Biggest gainers and losers in mindshare |

### Events

| Tool | What it does |
|------|-------------|
| `kaito_events` | Upcoming catalyst events for a token (unlocks, mainnet releases, governance votes) |
| `kaito_tweet_engagement_info` | Detailed engagement metrics for a specific tweet |

---

## Setup

### Step 1 — Get a Kaito API key

1. Go to [kaito.ai](https://kaito.ai) and sign up
2. Navigate to your dashboard → API access
3. Copy your API key

---

### Step 2 — Add to Claude Code

Add to `.mcp.json` in your project root:

```json
{
  "mcpServers": {
    "kaito": {
      "command": "npx",
      "args": ["-y", "kaito-mcp-server@latest"],
      "env": {
        "KAITO_API_KEY": "your-api-key-here"
      }
    }
  }
}
```

Restart Claude Code. The `kaito_*` tools will be available immediately.

> **nvm users:** Replace `npx` with the full path to your node bin. Run `/setup-skills-master-mcp` for a guided fix.

---

### Step 3 — Add to Claude.ai (optional)

Kaito doesn't expose a hosted MCP URL — it runs as a local process. For Claude.ai, use Claude Code instead, or check if Kaito offers a hosted endpoint via their dashboard.

---

## Rate limits

Kaito's API has a daily call budget that varies by plan. The [V3 Daily KOL](../../plugins/v3-daily-kol/) plugin uses ~6–10 Kaito calls per daily run, with an 80 calls/day budget across all agents.

Check your current usage at [kaito.ai/dashboard](https://kaito.ai/dashboard).
