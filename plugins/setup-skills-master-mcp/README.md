# setup-skills-master-mcp

Unlock skill discovery and installation directly inside Claude Code.

This skill sets up the [skills-master-mcp](https://github.com/davila7/skills-master-mcp) server — giving Claude access to a marketplace of community skills it can search, preview, and install on your behalf, without you ever leaving the conversation.

Once active, you can ask Claude things like:

> Find me a skill for web scraping
> Install the senior-architect skill
> What skills are available for crypto research?

Claude will search the marketplace, show you results, and install with one confirmation.

---

## Install

### Option 1 — One-liner (recommended)

Run this in your project directory:

```bash
curl -fsSL https://raw.githubusercontent.com/incyd/skills-master-mcp-setup-skill/main/install.sh | bash
```

Then restart Claude Code. The MCP server connects automatically.

---

### Option 2 — Via Claude Code marketplace

If you have the `claude-crypto-research-tools` marketplace and setup-skills-master-mcp plugin installed, type this directly in Claude Code:

```
/setup-skills-master-mcp
```

Claude will run the skill and walk you through the setup interactively.

To install the marketplace first:

```bash
/plugin marketplace add incyd/claude-crypto-research-tools
/plugin install setup-skills-master-mcp
```

---

### Option 3 — Manual

```bash
mkdir -p .claude/skills/setup-skills-master-mcp
curl -fsSL https://raw.githubusercontent.com/incyd/skills-master-mcp-setup-skill/main/.claude/skills/setup-skills-master-mcp/SKILL.md \
  -o .claude/skills/setup-skills-master-mcp/SKILL.md
```

---

## Why a dedicated installer?

Claude Code does not inherit your shell's PATH when it spawns MCP servers. If Node.js was installed via nvm, the MCP process cannot find `node` — even though your terminal can:

```
env: node: No such file or directory (exit 127)
```

The installer detects where your node binary actually lives and writes that path into `.mcp.json` via `env.PATH`. This is the only non-obvious step — everything else is standard.

---

## What the skill does

After the MCP server is running, the `setup-skills-master-mcp` skill gives Claude the ability to:

- Search the skills marketplace by keyword or description
- Preview a skill before installing it
- Install skills into `.claude/skills/` in your project
