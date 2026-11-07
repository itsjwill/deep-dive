---
name: deep-dive
version: 1.0.0
description: Run a real deep dive on any topic using the RECON research engine. Finds and scores the best YouTube videos, academic papers and podcasts (plus one contrarian source), loads them into a NotebookLM notebook, interrogates that notebook with targeted questions, then delivers an opinionated, cited brief. Falls back to direct search when NotebookLM is down. Trigger on "deep dive", "deep dive on X", "research X", "ultra think", "think the impossible", "/deep-dive", or any request to go deep on a topic before deciding or building.
---

# Deep Dive

A deep dive is not a web search. It is: find the best sources, read them for real, attack the idea from the other side, and come back with an opinion and a plan.

Engine: [RECON](https://github.com/itsjwill/RECON) (MCP server `auto-research`) plus [notebooklm-mcp](https://www.npmjs.com/package/notebooklm-mcp) for querying the notebook.

## Rule zero: act first

When a trigger phrase appears, the FIRST action is the research call. No text first. No clarifying questions. Pull the topic out of context. If the topic is the user's own code, bot or business, research the external strategies and best practices for that domain, then apply them to their situation in the synthesis.

## Step 1: Pick the settings

| Trigger | sources | num_sources | Extra |
|---|---|---|---|
| "research X" | `["youtube"]` | 5 | |
| "deep dive X" | `["youtube", "papers"]` | 6 | |
| "ultra think X" | `["all"]` (youtube + papers + podcasts) | 10 (max) | Ask 6+ notebook questions |
| "think the impossible X" | `["all"]` | 10 | Also run `suggest_research()` for adjacent ideas |

**Mode:**
- `mode="expert"` for trading, bots, strategies, or any niche where practitioners beat influencers. Inverts popularity scoring so small channels with real substance win.
- `mode="general"` for everything else (business, marketing, learning, product).

**Topic string:** short and searchable, 3-8 words. "liquidation cascade trading strategy", not a paragraph.

## Step 2: Run RECON

```
research(topic="<topic>", sources=[...], num_sources=N, mode="<mode>")
```

Takes 1-4 minutes. It searches, scores every source for substance, credibility and transcript depth, swaps the weakest slot for a contrarian source ("<topic> problems / criticism / risks"), builds a NotebookLM notebook, registers it in the library, and updates the knowledge graph.

Read the JSON it returns: `status`, `notebook_url`, `notebook_id`, `sources` (title, url, type, score, channel, contrarian flag), `connected_notebooks`.

## Step 3: Handle failure (do not stall)

| Result | Do this |
|---|---|
| `status: success` | Go to Step 4 |
| `status: partial` | Go to Step 4, note which sources failed |
| Auth / "New notebook" button / login error | Run `notebooklm_login()` to check. If not logged in, tell the user in ONE line to run `notebooklm_login(interactive=true)` (opens Chrome to sign in), then continue with the fallback below. Do not use notebooklm-mcp `setup_auth`, it does not detect the rebranded notebook.google.com site |
| No sources found | Retry once with a broader topic string, then fallback |

**Fallback (no notebook):** run `search_videos(topic)` and `search_papers(topic)` from RECON plus web search in parallel. Read the top results directly (transcripts, abstracts, pages). Still include a contrarian search. Still deliver the full brief in Step 5. Tell the user in one line that NotebookLM was down and this ran on direct search.

## Step 4: Interrogate the notebook

Use notebooklm-mcp `ask_question(question, notebook_url=<notebook_url>)`. Ask questions that pull substance, not summaries. Run them one after another in the same session. Minimum set:

1. **Mechanism:** "What is the core mechanism that makes this work? Explain it step by step with specifics."
2. **Practitioner playbook:** "What exactly do the people getting real results do differently? List concrete tactics, numbers, settings, thresholds."
3. **Contrarian:** "Where does this fail? What are the biggest risks, criticisms and mistakes the sources mention?"
4. **Numbers:** "List every concrete number, benchmark, cost, result or statistic in the sources, with which source said it."
5. **Application:** "Given <user's actual situation in one sentence>, what should they do first and what should they avoid?"

Ultra think / think the impossible: add 1-3 more, such as "What do the sources disagree on?", "What is the non-obvious insight most people miss?", "What would a 10x version of this look like?"

If an answer is vague, ask one follow-up that names the specific thing you need.

## Step 5: Deliver the brief

Opinion first. The user wants a verdict, not a literature review.

```
## Deep Dive: <topic>

**Bottom line:** <2-3 sentences: what is true, what to do, how confident>

### What actually works
- <finding> [source]
- ...

### The numbers
| Metric | Value | Source |

### Where it breaks (contrarian view)
- <risk / failure mode> [source]

### What this means for you
<applied to the user's real situation: first move, what to skip, what to test>

### Sources
| # | Title | Type | Score | Why it made the cut |
(mark the contrarian source)

Notebook: <notebook_url>
```

Cite the source for every claim that matters. If sources disagree, say so and say which one you trust and why. Flag anything that sounds like hype with no evidence.

## Step 6: Offer next moves (one line each, pick what fits)

- "Want me to keep querying the notebook?" (it stays live)
- `suggest_research()` for what to dig into next, based on knowledge gaps
- `knowledge_map()` to see how this connects to earlier research
- Later, once the user acts on it: `track_edge(notebook_id, result="edge" | "no_edge" | "negative_edge", notes)` so RECON learns which sources and channels pay off. `edge_report()` shows that ROI.
- `rate_research(notebook_id, 1-5)` if the user reacts strongly to the quality.

## Tool reference

| Tool | Server | Use |
|---|---|---|
| `research` | auto-research | Main pipeline: search, score, notebook, graph |
| `search_videos` / `search_papers` | auto-research | Fallback or quick look without a notebook |
| `list_research_notebooks` | auto-research | Find a past deep dive to reuse instead of redoing it |
| `suggest_research` / `knowledge_map` | auto-research | What to research next, how topics connect |
| `track_edge` / `edge_report` / `rate_research` | auto-research | Feedback loop that improves future scoring |
| `notebooklm_login` | auto-research | Check or refresh the NotebookLM login |
| `ask_question` | notebooklm-mcp | Query the notebook |

**Before a new deep dive**, if the topic sounds familiar, run `list_research_notebooks()` and query the existing notebook first. Only rerun `research()` if it is stale or thin.

## Setup

1. Install RECON: `git clone https://github.com/itsjwill/RECON && cd RECON && python3 -m venv .venv && .venv/bin/pip install -e .` Set `YOUTUBE_API_KEY` in its `.env`.
2. Install notebooklm-mcp: `npm i -g notebooklm-mcp`
3. Add both to your MCP config (`~/.mcp.json` or `claude mcp add`):
   ```json
   "auto-research": { "command": "<RECON>/.venv/bin/python3", "args": ["<RECON>/run_server.py"] },
   "notebooklm-mcp": { "command": "npx", "args": ["notebooklm-mcp"] }
   ```
4. Sign in once: call `notebooklm_login(interactive=true)` and log in to Google in the Chrome window.
5. Install this skill: `git clone https://github.com/itsjwill/deep-dive ~/.claude/skills/deep-dive`
