# Netrows X/Twitter MCP Connector

Give Claude direct access to X/Twitter data through the Netrows API — no X developer account or bearer token required. Just bring a Netrows API key.

Deployed on **Render** · Connects to **Claude Code or Claude.ai** via MCP

---

## What you can do

Once connected, ask Claude naturally:

```
What are people on X saying about Solana this week?
```
```
Find the most-liked posts about $ETH in the last 24 hours
```
```
Get the latest tweets from @VitalikButerin and summarize them
```
```
Pull this thread and break down the arguments: [tweet URL]
```
```
Search X for posts about the Bybit hack, sorted by engagement
```
```
Who are the top followers of this account?
```

---

## Available Tools

| Tool | What it does |
|------|-------------|
| `get_user_info` | Profile info, follower counts, verification status |
| `get_user_tweets` | Recent posts from any account |
| `search_users` | Find accounts by keyword |
| `get_followers` | List of an account's followers |
| `get_following` | Accounts an account follows |
| `get_verified_followers` | Verified/notable followers only |
| `get_user_mentions` | Recent mentions of an account |
| `search_tweets` | Keyword search across recent X posts |
| `get_tweet_replies` | Replies to a specific tweet |
| `get_tweet_quotes` | Quote tweets for a post |
| `get_tweet_retweeters` | Who retweeted a post |
| `get_thread` | Full conversation thread from any tweet |
| `get_list_members` | Members of an X list |
| `get_community_tweets` | Posts from an X Community |

---

## Setup (~2 minutes)

### Step 1 — Get a Netrows API key

1. Go to [netrows.com](https://www.netrows.com) and sign up
2. Navigate to your dashboard → API keys
3. Copy your key — it starts with `pk_live_...`

---

### Step 2 — Connect to Claude

Add the MCP server with your API key as a query parameter:

```
https://netrows-x-mcp.onrender.com/mcp?netrows_api_key=pk_live_YOUR_KEY
```

**Claude Code** — add to `.mcp.json` in your project:

```json
{
  "mcpServers": {
    "netrows-x": {
      "url": "https://netrows-x-mcp.onrender.com/mcp?netrows_api_key=pk_live_YOUR_KEY"
    }
  }
}
```

**Claude.ai** — go to Settings → Connectors → Add custom connector, paste the URL above.

---

## Privacy & Logging

Every tool call (tool name, arguments, response, timestamp) is logged to a private Postgres database for debugging. API keys are never stored in the database — users are identified by a one-way hash of their key.

---

## Note on cold starts

The server runs on Render's free tier and spins down after ~15 minutes of inactivity. The first request after idle may be slow (~10–15s). Pre-warm by hitting:

```
https://netrows-x-mcp.onrender.com/health
```
