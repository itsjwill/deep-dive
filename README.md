# DEEP DIVE — Claude Code Skill for Real Research

### Free alternative to Perplexity Deep Research, ChatGPT Deep Research, Elicit and Consensus

<div align="center">

![License](https://img.shields.io/badge/License-MIT-green?style=for-the-badge)
![Updated](https://img.shields.io/badge/Updated-2026-blue?style=for-the-badge)
![Claude Code](https://img.shields.io/badge/Claude_Code-Skill-purple?style=for-the-badge)
![Powered by RECON](https://img.shields.io/badge/Powered_by-RECON-black?style=for-the-badge)

</div>

> Say "deep dive on X" in Claude Code. It finds the best YouTube videos, academic papers and podcasts, scores every one for substance and credibility, adds a contrarian source on purpose, loads them all into a NotebookLM notebook, interrogates it, and hands you a cited verdict with a plan.

---

## Why this exists

"Deep research" tools skim the top 20 web pages and summarize them. You get the consensus of SEO content.

DEEP DIVE reads the sources practitioners actually learn from: long-form videos with real transcripts, peer-reviewed papers, expert podcasts. Then it argues with itself before it answers.

## How it works

```
1. SEARCH       YouTube + OpenAlex papers (474M+) + podcasts
2. SCORE        substance, credibility, transcript depth (expert mode favors practitioners over influencers)
3. CONTRARIAN   one slot always goes to "problems / risks / criticism"
4. NOTEBOOK     every source loaded into a queryable NotebookLM notebook
5. INTERROGATE  mechanism, playbook, failure modes, every number, your situation
6. VERDICT      bottom line first, cited findings, risks, next move
7. COMPOUND     knowledge graph links every dive; edge tracking learns which sources pay off
```

## Trigger phrases

| Say | You get |
|---|---|
| `research X` | 5 YouTube sources, quick brief |
| `deep dive X` | YouTube + papers, full brief |
| `ultra think X` | YouTube + papers + podcasts, 10 sources, 6+ notebook questions |
| `think the impossible X` | All of the above + adjacent ideas you have not considered |

## Vs. the paid tools

| | DEEP DIVE | Perplexity Pro | ChatGPT Deep Research | Elicit |
|---|---|---|---|---|
| Price | Free | $20/mo | $20-200/mo | $12+/mo |
| Video transcripts | Yes | No | No | No |
| Academic papers | Yes | Partial | Partial | Yes |
| Forced contrarian source | Yes | No | No | No |
| Queryable notebook after | Yes | No | No | No |
| Learns from your results | Yes | No | No | No |

## Install

Requires [RECON](https://github.com/itsjwill/RECON) and [notebooklm-mcp](https://www.npmjs.com/package/notebooklm-mcp). Full steps in [SKILL.md](SKILL.md#setup).

```bash
git clone https://github.com/itsjwill/deep-dive ~/.claude/skills/deep-dive
```

Then in Claude Code: `deep dive on <anything>`.

## License

MIT
