# Tool reference

| Tool | Server | Use |
|---|---|---|
| `research(topic, num_sources, sources, mode)` | auto-research (RECON) | Main pipeline: search, score, contrarian swap, NotebookLM notebook, knowledge graph |
| `search_videos(query, limit)` | auto-research | YouTube search with transcript and credibility scoring, no notebook |
| `search_papers(query, limit)` | auto-research | OpenAlex search (474M+ papers), citation counts, open access status |
| `list_research_notebooks()` | auto-research | Past deep dives: IDs, topics, URLs. Check before rebuilding |
| `suggest_research()` | auto-research | Topics to research next, from gaps in the knowledge graph |
| `knowledge_map()` | auto-research | How concepts connect across research sessions |
| `track_edge(notebook_id, result, notes)` | auto-research | Record whether a dive produced real results: `edge`, `no_edge`, `negative_edge` |
| `edge_report()` | auto-research | Research ROI by channel, topic and source type |
| `rate_research(notebook_id, rating)` | auto-research | 1-5 quality rating, tunes future scoring |
| `notebooklm_login(interactive)` | auto-research | Check the NotebookLM login; `interactive=true` opens Chrome to sign in |
| `ask_question(question, notebook_url \| notebook_id, session_id)` | notebooklm-mcp | Query a notebook. Reuse `session_id` for follow-ups |

`sources` accepts `"youtube"`, `"papers"`, `"podcasts"`, or `"all"`. `num_sources` caps at 10. Podcasts need `PODCAST_INDEX_KEY` / `PODCAST_INDEX_SECRET` in RECON's `.env`.
