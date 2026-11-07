---
name: deep-dive
version: 1.1.0
description: Run a real deep dive on any topic. Uses the RECON research engine to find and score the best YouTube videos, academic papers and podcasts (plus a forced contrarian source), loads them into a NotebookLM notebook, interrogates it, verifies the key citations, then delivers an opinionated, cited verdict with a plan. Works without RECON in lite mode (web search + direct reading). Trigger on "deep dive", "deep dive on X", "research X", "ultra think", "think the impossible", "/deep-dive", or any request to go deep on a topic before deciding or building.
---

# Deep Dive

A deep dive is not a web search. Find the best sources, read them for real, attack the idea from the other side, check the citations, and come back with an opinion and a plan.

Engine: [RECON](https://github.com/itsjwill/RECON) (MCP server `auto-research`) plus [notebooklm-mcp](https://www.npmjs.com/package/notebooklm-mcp). Without them, run lite mode (Step 3). Setup: [references/setup.md](references/setup.md). All tools: [references/tools.md](references/tools.md).

## Rule zero: act first

When a trigger phrase appears, the FIRST action is research. No text first. No clarifying questions. Pull the topic out of context. If the topic is the user's own code, bot or business, research external strategies and best practices for that domain, then apply them to their situation in the brief.

## Step 1: Check the library and the tools

- If the `research` tool is not in your tool list, search for it once (e.g. ToolSearch `+auto-research research`). Still missing means the server is not connected: go to lite mode (Step 3) and say so in one line.
- If the topic sounds familiar, run `list_research_notebooks()`. A matching recent notebook gets queried (Step 4) instead of rebuilt.

## Step 2: Pick settings and run RECON

**Depth by trigger:**

| Trigger | num_sources | Notebook questions | Extra |
|---|---|---|---|
| "research X" | 5 | 3 | |
| "deep dive X" | 6 | 5 | |
| "ultra think X" | 10 (max) | 6-8 | Parallel angle agents (Step 4b) |
| "think the impossible X" | 10 | 6-8 | Step 4b + `suggest_research()` for adjacent ideas |

**Sources by domain** (papers are noise on fast-moving tech, gold on science):

| Topic type | sources | Add in parallel |
|---|---|---|
| Tech, tools, AI, software, marketing tactics | `["youtube"]` | Web search + GitHub search (recency matters) |
| Science, health, psychology, economics | `["youtube", "papers"]` | |
| Trading, investing, niche craft | `["youtube", "podcasts"]` with `mode="expert"` | |
| Business strategy, general learning | `["youtube", "papers", "podcasts"]` | |

**Mode:** `mode="expert"` for trading, bots, strategies or any niche where practitioners beat influencers (inverts popularity scoring). `mode="general"` otherwise.

**Topic string:** 3-8 searchable words. "liquidation cascade trading strategy", not a paragraph.

```
research(topic="<topic>", sources=[...], num_sources=N, mode="<mode>")
```

Takes 1-4 minutes: searches, scores each source for substance, credibility and transcript depth, swaps the weakest slot for a contrarian source, builds a NotebookLM notebook, updates the knowledge graph. Read `status`, `notebook_url`, `notebook_id`, `sources`, `connected_notebooks`.

## Step 3: Handle failure, or run lite mode

| Result | Do this |
|---|---|
| `success` / `partial` | Step 4 (note failed sources) |
| Login / "signed out" / "New notebook" error | Run `notebooklm_login()`. If signed out, tell the user in ONE line to run `notebooklm_login(interactive=true)`, then lite mode. Never use notebooklm-mcp `setup_auth` (does not detect notebook.google.com) |
| No sources | Retry once with a broader topic string, then lite mode |

**Lite mode** (no notebook): in parallel, run `search_videos` / `search_papers` if RECON is connected, plus 3-5 web searches: the topic, "<topic> best practices / how practitioners do it", "<topic> problems / criticism / doesn't work", and "<topic> data / benchmark / results". Fetch and read the top 5-8 pages or transcripts directly. Keep at least one contrarian source. Then Steps 5-7 exactly as normal.

## Step 4: Interrogate

### 4a. Notebook questions

Use notebooklm-mcp `ask_question(question, notebook_url=<url>)`, one after another in the same session. Pull substance, not summaries:

1. **Mechanism:** "What is the core mechanism that makes this work? Step by step, with specifics."
2. **Practitioner playbook:** "What do the people getting real results do differently? Concrete tactics, numbers, settings, thresholds."
3. **Contrarian:** "Where does this fail? Biggest risks, criticisms and mistakes in the sources."
4. **Numbers:** "Every concrete number, benchmark, cost or result in the sources, each with the source that said it and a short exact quote."
5. **Application:** "Given <user's situation in one sentence>, what should they do first and what should they avoid?"

Deeper triggers add: "What do the sources disagree on, and who has better evidence?", "What non-obvious insight do most people miss?", "What would a 10x version look like?"

Vague answer = one follow-up that names the exact thing you need.

### 4b. Parallel angle agents (ultra think / think the impossible)

Spawn 3 subagents at once, each with a narrow brief and its own searches:
- **Practitioner:** what people with real results do, with numbers.
- **Evidence:** studies, benchmarks, data, official docs.
- **Contrarian:** failures, criticism, who says this does not work and why.

Each returns claims as `claim | source URL | exact supporting quote`. You merge. Do not let subagents write the brief.

## Step 5: Verify before you write

Citations make weak claims look checked. Research shows deep-research tools fabricate 3-13% of URLs and only 40-80% of citations actually support the claim. So:

1. Pick the 5 claims the verdict depends on most.
2. For each, confirm the source says it: fetch the page, or ask the notebook "Quote the exact passage that says <claim>".
3. Supported = keep. Not found = cut it or mark it `(unverified)`. Contradicted = flip it into the contrarian section.
4. Only cite URLs that a tool returned in this session. Never write a URL from memory.

## Step 6: Deliver the brief

Opinion first. A verdict, not a literature review.

```
## Deep Dive: <topic>

**Bottom line:** <2-3 sentences: what is true, what to do, confidence high/medium/low and why>

### What actually works
- <finding> [source]

### The numbers
| Metric | Value | Source |

### Where it breaks (contrarian view)
- <risk / failure mode> [source]

### Where sources disagree
<who says what, which side has better evidence> (skip if none)

### What this means for you
<applied to the user's real situation: first move, what to skip, what to test>

### Sources
| # | Title | Type | Score | Why it made the cut |
(mark the contrarian source; mark verified claims' sources)

Notebook: <notebook_url>  |  Verified: <n>/5 key claims
```

Flag hype with no evidence. If the evidence is thin, say the confidence is low. Do not pad.

## Step 7: Offer next moves (one line each, only what fits)

- Keep querying the notebook (it stays live).
- `suggest_research()` for knowledge gaps; `knowledge_map()` for connections to past dives.
- After the user acts on it: `track_edge(notebook_id, "edge" | "no_edge" | "negative_edge", notes)`. RECON learns which sources pay off; `edge_report()` shows the ROI.

## Gotchas

- **Empty `key_concepts` on every video** = transcript extraction is failing (RECON < 1.0.1 with youtube-transcript-api >= 1.0). Scores then rank by title and channel only. Update RECON.
- **NotebookLM moved to notebook.google.com.** Logins lapse after ~12 idle days and Google may ask "Verify it's you". Fix with `notebooklm_login(interactive=true)`.
- **"Profile is open in another process"** = a notebooklm-mcp query holds the Chrome profile. Wait a minute and retry.
- **Papers off-topic** (e.g. finance papers for a coding question): OpenAlex is weak on new tooling. Drop papers, use web + GitHub.
- **More tool calls does not mean more accuracy.** Fact-check accuracy drops as research sprawls. Five well-read sources beat thirty skimmed ones.
- **One contrarian source is not balance.** On debatable topics, actively state the strongest opposing case.
