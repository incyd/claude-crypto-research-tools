# V3 Daily KOL

A four-agent Claude pipeline that runs daily to keep you in the highest-signal X/Twitter conversations in your niche — and helps you post on the right topics at the right time.

Every morning it pulls the most relevant posts from your topic clusters and thought leader feeds, scores them by signal strength, generates 20+ engagement replies in your voice, and recommends what you should post today with supporting data. Every Sunday it reviews what worked, proposes knowledge base updates, and flags what to change.

The goal: grow your audience by being present in conversations that matter, with takes that are actually yours.

---

## What you get each day

**`engagement_replies.xlsx`** — 20+ ready-to-post replies, each with:
- The post you're replying to (ID, author, text)
- The angle used (cross-pollinated insight, own perspective, or research-backed)
- A confidence score (0–10)
- The reply text, humanized and voice-matched
- CTA included/excluded decision with reason

**`own_post_recs.xlsx`** — 2–3 original post recommendations, each with:
- Topic and angle
- `rec_score` (signal strength × convergence × coverage gap × topic fit × timeliness)
- Supporting data points pulled from Tavily
- Draft copy in your voice

**`web3_knowledge_base.md`** — a growing narrative intelligence file that tracks active narratives, fading narratives, and what's on the rise in your space. Updated weekly via Agent ④ proposals (you review before anything changes).

---

## The four agents

```
Agent ① Onboarding  (Opus 4.6)  — run once per account
         ↓
Agent ② Daily Pull  (Sonnet 4.6) — runs daily
         ↓
Agent ③ Analytics   (Opus 4.6)  — runs daily, chained after ②
         ↓
Agent ④ Weekly      (Opus 4.6)  — runs Sundays
```

### Agent ① — Onboarding
Run once. Point it at your X handle and it pulls 200 of your recent posts, extracts your topics, keyword combinations, writing voice, competitors, and thought leaders. Writes `account_profile.md` — the source of truth every other agent reads.

### Agent ② — Daily Pull
Three parallel data modes every morning:

- **Mode A (Topics)** — Searches your keyword combos via Kaito AI (primary), falls back to X API search. Clusters results into themes. Scores each post by engagement signal.
- **Mode B (Thought Leaders)** — Pulls the last 24h from your tracked KOL list (~23 accounts). Filters for freshness and topic relevance.
- **Mode C (Own Posts)** — Syncs your recent posts into `own_posts.jsonl` so Agent ③ and ④ can track what you actually published.

All posts normalized to a 23-field canonical schema. Appended to `daily_posts.json` (date-keyed, never overwritten).

### Agent ③ — Analytics & Content
Reads today's posts, ranks themes by signal, generates replies and post recommendations.

Signal scoring:
```
engagement_score = (replies × 27) + (retweets × 20) + (quotes × 24) + (likes × 1) + (bookmarks × 4)
signal_score     = engagement_score / views × 1000
```
Convergence bonus (+2.0) when the same narrative appears across multiple independent sources on the same day.

Reply generation uses a three-source angle model:
- **70% cross-pollinated** — take an insight from one creator's post, apply it as a reply on another's
- **20% own-perspective** — drawn from your historical post corpus
- **10% research-informed** — live Tavily enrichment per theme

Every reply goes through a mandatory humanizer pass. Em dashes are zero tolerance (the #1 AI writing tell). Output must read as yours.

### Agent ④ — Weekly Learning
Runs Sundays. Reviews the past 7 days of data — which angles you engaged with, which recommendations you actually posted, which keyword combos are producing noise. Outputs three proposal files (all require your review before anything changes):

1. `knowledge_edit_proposals/` — narrative stage transitions, new narratives to add, content updates
2. `effectiveness_reports/` — keyword performance, angle distribution, recommendation adoption rate
3. `claude_md_proposals/` — system tuning suggestions, keyword replacements, operational learnings

Nothing updates automatically. You're always in control of what gets applied.

---

## Required MCP connectors

| Connector | Used for | Priority |
|-----------|----------|----------|
| **Kaito AI** | Topic discovery (Mode A) — mindshare, sentiment, advanced search | Primary |
| **X API (Connector B)** | Tweet fetching, TL timelines, own-post sync | Primary |
| **Tavily** | Web enrichment per theme, own-post data sourcing | Required |
| **X API (Connector A)** | Fallback search only — expensive, last resort | Fallback |

Connector B setup: add your X bearer token via the [Native X AI MCP](../connectors/x-research-mcp/) or [Netrows X MCP](../connectors/netrows-x-mcp/) connector. Kaito and Tavily are configured via their own connectors — see [connectors/](../connectors/).

---

## Setup

### Step 1 — Run Agent ① (Onboarding)

Open Claude with the `v3_Daily_KOL` project. Tell it:

```
Run Agent ① onboarding for @yourtwitterhandle
```

This takes ~10 minutes. It reads 200 of your recent posts and builds your account profile. You only do this once.

### Step 2 — Run Agent ② + ③ daily

```
Run Agent ② daily pull
```

Claude will run Modes A, B, and C in sequence, then automatically chain into Agent ③. Your `engagement_replies.xlsx` and `own_post_recs.xlsx` will be updated by the end of the run.

### Step 3 — Run Agent ④ on Sundays

```
Run Agent ④ weekly learning
```

Review the proposals it generates in `weekly/`. Apply what makes sense, skip what doesn't.

---

## Data architecture

All data files are append-only. Nothing is ever overwritten — you have a complete history from day one.

| File | Written by | What it contains |
|------|-----------|-----------------|
| `account_data/profile/account_profile.md` | Agent ① | Topics, keywords, voice, thought leaders, competitors |
| `account_data/data/daily_posts.json` | Agent ② | Date-keyed daily post batches (23-field schema) |
| `account_data/data/own_posts.jsonl` | Agent ② | Your published posts (deduped by post_id) |
| `account_data/analytics/analytics_history.jsonl` | Agent ③ | One record per day: theme counts, angle mix, confidence avg |
| `account_data/output/engagement_replies.xlsx` | Agent ③ | All generated replies with metadata |
| `account_data/output/own_post_recs.xlsx` | Agent ③ | Daily post recommendations (archived before each run) |
| `account_data/knowledge/web3_knowledge_base.md` | Agent ④ | Narrative intelligence (proposals only, user-approved) |
| `weekly/*/` | Agent ④ | Weekly proposals for KB edits, effectiveness, system tuning |

---

## Rate limits

| Connector | Budget |
|-----------|--------|
| Kaito | 80 calls/day across all agents |
| X API (Connector B) — `get_user_tweets` | 23 calls/day for Mode B |
| Tavily | 10 calls per Agent ③ run |
| X API (Connector A) — search | 10 calls/day max, last resort only |

---

> Currently configured for `@incyd__` (DeFi yields, AI tooling, RWA). Agent ① re-onboards for any account — the system is instance-agnostic.
