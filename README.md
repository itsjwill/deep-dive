# DEEP DIVE — Claude Code Skill for Verified Research

### Free alternative to Perplexity Deep Research, ChatGPT Deep Research, Elicit and Consensus

<div align="center">

![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)
![Version](https://img.shields.io/badge/version-1.2.0-blue?style=for-the-badge)
![Claude Code](https://img.shields.io/badge/Claude_Code-Plugin_+_Skill-purple?style=for-the-badge)
![Powered by RECON](https://img.shields.io/badge/Powered_by-RECON-black?style=for-the-badge)

</div>

> Say "deep dive on X" in Claude Code. It finds the best YouTube videos, academic papers, podcasts and X threads, scores each one for substance and credibility, forces in a contrarian source, loads them into a NotebookLM notebook, interrogates it, **checks that the key citations actually say what it claims**, and hands you a verdict with a plan.

```
/plugin install deep-dive --marketplace itsjwill/deep-dive
```

---

## Why this exists

"Deep research" tools skim the top web pages and summarize them. Worse, they cite badly: a 2026 study found deep-research agents fabricate **3-13% of their URLs** ([Rao et al.](https://arxiv.org/abs/2604.03173)), and Microsoft's DeepTRACE audit found only **40-80% of citations actually support the claim** ([ICLR 2026](https://iclr.cc/virtual/2026/poster/10009560)).

DEEP DIVE reads the sources practitioners actually learn from (long-form transcripts, peer-reviewed papers, expert podcasts), argues with itself, and verifies its 5 most important claims against the source text before it answers. Unverified claims get cut or labeled.

## How it works

```
1. ROUTE        pick sources by topic: tech = video + X + web + GitHub, science = papers, trading = expert mode + podcasts
2. SEARCH       YouTube + OpenAlex papers (474M+) + podcasts + X (free, no account)
3. SCORE        substance, credibility, transcript depth (expert mode favors practitioners over influencers)
4. CONTRARIAN   one slot always goes to "problems / risks / criticism"
5. NOTEBOOK     every source loaded into a queryable NotebookLM notebook
6. INTERROGATE  mechanism, playbook, failure modes, every number with a quote, your situation
7. VERIFY       5 load-bearing claims checked against the source text; never cites a URL from memory
8. VERDICT      bottom line + confidence first, cited findings, where sources disagree, next move
9. COMPOUND     knowledge graph links every dive; edge tracking learns which sources pay off
```

`ultra think` also splits the work across 3 parallel agents (practitioner, evidence, contrarian), the multi-agent pattern behind the big commercial research tools.

## Trigger phrases

| Say | You get |
|---|---|
| `research X` | 5 sources, quick brief |
| `deep dive X` | 6 sources, 5 notebook questions, full verified brief |
| `ultra think X` | 10 sources, 3 parallel angle agents, 6-8 questions |
| `think the impossible X` | All of the above + adjacent ideas you have not considered |

## Lite mode: works with zero setup

No RECON or NotebookLM? The skill still runs: parallel web searches (topic, practitioner, contrarian, data), reads the top pages directly, verifies, and delivers the same brief. Install RECON when you want scored video transcripts, papers and persistent notebooks.

## Vs. the paid tools

| | DEEP DIVE | Perplexity Pro | ChatGPT Deep Research | Elicit |
|---|---|---|---|---|
| Price | Free | $20/mo | $20-200/mo | $12+/mo |
| Video transcripts | Yes | No | No | No |
| X/Twitter practitioner threads | Yes (free) | Partial | No | No |
| Academic papers | Yes | Partial | Partial | Yes |
| Forced contrarian source | Yes | No | No | No |
| Verifies its own citations | Yes | No | No | Partial |
| Queryable notebook after | Yes | No | No | No |
| Learns from your results | Yes | No | No | No |

## What a brief looks like (abridged example)

```
## Deep Dive: improving a Claude Code skill repo

**Bottom line:** The skill design is fine; the upstream engine had a silent bug
killing transcript scoring. Fix that, package as a plugin, add citation checks.
Confidence: high on the bug, medium on discoverability gains.

### What actually works
- Plugin + marketplace.json with source "./" gives one-command install [code.claude.com]
- Gotchas sections are often the highest-value part of a skill [skill authoring guide]

### Where it breaks (contrarian view)
- Deep-research agents fabricate 3-13% of URLs [Rao et al. 2026]
...
```

## Install

**Claude Code plugin** (auto-updates):
```
/plugin install deep-dive --marketplace itsjwill/deep-dive
```
or `/plugin marketplace add itsjwill/deep-dive` then `/plugin install deep-dive@deep-dive`.

**Any agent (skills.sh):**
```bash
npx skills add itsjwill/deep-dive
```

**Manual:**
```bash
git clone https://github.com/itsjwill/deep-dive ~/deep-dive
ln -s ~/deep-dive/skills/deep-dive ~/.claude/skills/deep-dive
```

Full mode needs [RECON](https://github.com/itsjwill/RECON) v1.0.1+ and [notebooklm-mcp](https://www.npmjs.com/package/notebooklm-mcp). Steps in [setup.md](skills/deep-dive/references/setup.md).

## Repo layout

```
.claude-plugin/
  plugin.json          plugin manifest
  marketplace.json     single-plugin marketplace (source: ./)
skills/deep-dive/
  SKILL.md             the workflow
  references/
    setup.md           RECON + NotebookLM install
    tools.md           every tool and parameter
```

## Changelog

- **1.2.0**: X/Twitter search (free via web search, no account or API key), routed to tech, AI, trading and business topics; X-specific gotchas; Reddit status documented.
- **1.1.0**: citation verification step, lite mode (no setup), source routing by topic, parallel angle agents for ultra think, gotchas section, plugin + marketplace packaging, skills.sh support.
- **1.0.0**: initial release.

## License

MIT
