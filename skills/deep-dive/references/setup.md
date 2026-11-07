# Setup

Lite mode needs nothing beyond Claude Code's web search. Full mode (scored sources + NotebookLM notebook) needs RECON and notebooklm-mcp.

## 1. RECON (v1.0.1 or later)

```bash
git clone https://github.com/itsjwill/RECON && cd RECON
python3 -m venv .venv && .venv/bin/pip install -e .
echo "YOUTUBE_API_KEY=your_key" > .env   # YouTube Data API v3 key (free quota)
```

Optional in `.env`: `PODCAST_INDEX_KEY`, `PODCAST_INDEX_SECRET` (podcastindex.org, free) for podcast sources.

## 2. notebooklm-mcp

```bash
npm i -g notebooklm-mcp
```

## 3. Register both MCP servers

```bash
claude mcp add auto-research -- /path/to/RECON/.venv/bin/python3 /path/to/RECON/run_server.py
claude mcp add notebooklm-mcp -- npx notebooklm-mcp
```

Or in `~/.mcp.json`:

```json
{
  "mcpServers": {
    "auto-research": { "command": "/path/to/RECON/.venv/bin/python3", "args": ["/path/to/RECON/run_server.py"] },
    "notebooklm-mcp": { "command": "npx", "args": ["notebooklm-mcp"] }
  }
}
```

## 4. Sign in to NotebookLM once

In Claude Code: "run notebooklm_login with interactive=true". A Chrome window opens; sign in to Google. RECON and notebooklm-mcp share that login. Requires Google Chrome installed.

## 5. Install the skill

Pick one:

```bash
# Claude Code plugin (auto-updates on version bumps), one step on 2.1.275+
/plugin install deep-dive --marketplace itsjwill/deep-dive
# or two steps
/plugin marketplace add itsjwill/deep-dive
/plugin install deep-dive@deep-dive

# Any agent, via skills.sh
npx skills add itsjwill/deep-dive

# Manual
git clone https://github.com/itsjwill/deep-dive ~/deep-dive
ln -s ~/deep-dive/skills/deep-dive ~/.claude/skills/deep-dive
```

Restart Claude Code, then say: `deep dive on <anything>`.
