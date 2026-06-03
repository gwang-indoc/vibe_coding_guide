# Graphify: Codebase Knowledge Graph Tool

> Sources: safishamsi/graphify (GitHub), Unknown
> Raw: [Graphify — Codebase Knowledge Graph Tool](../../raw/ai-tools/2026-06-03-graphify-codebase-knowledge-graph-tool.md)

## Overview

Graphify is an AI coding assistant skill (Python, MIT, 58.8k stars) that converts a project folder into a queryable knowledge graph. It outputs `graph.html` (interactive visual), `GRAPH_REPORT.md` (written analysis), and `graph.json` (machine-queryable data). The graph captures semantic relationships across code, docs, schemas, PDFs, images, and video — identifying highly-connected "god nodes" and cross-module dependencies. It integrates with most major AI coding assistants including Claude Code, Cursor, Gemini CLI, and Aider.

## How It Works

**Two-phase extraction:**

1. **Code** — parsed locally via tree-sitter AST across 33 languages. No data leaves the machine.
2. **Docs / multimedia** — sent to a configured LLM backend (Claude, Gemini, OpenAI, DeepSeek, Ollama, AWS Bedrock). Ollama enables fully local operation.

Nodes are tagged with confidence levels: `EXTRACTED` (parsed from source), `INFERRED` (LLM-derived relationship), or `AMBIGUOUS` (uncertain). Community detection uses the Leiden algorithm to cluster related concepts.

## Outputs

| File | Purpose |
|------|---------|
| `graph.html` | Interactive visual explorer |
| `GRAPH_REPORT.md` | Written analysis: god nodes, surprising connections |
| `graph.json` | Structured queryable graph data |

Additional export formats: Mermaid call-flow diagrams (`callflow-html`), Obsidian vault, Neo4j/Cypher, MCP stdio server.

## Key Commands

```bash
uv tool install graphifyy
graphify install
/graphify .                                # build graph
/graphify . --update                       # re-extract changed files only
/graphify query "what connects X to Y?"   # semantic search
graphify export callflow-html              # Mermaid architecture diagram
graphify prs --triage                      # AI-ranked PR review queue
```

## Integration Points

- **Git hooks** — auto-rebuild graph on commit
- **PR dashboard** — CI state, review status, graph impact per PR
- **Cross-project merging** — combine multiple repo graphs into a global graph
- **MCP stdio server** — structured programmatic access to the graph
- **Neo4j export** — Cypher queries against the full graph

## Configuration

`.graphifyignore` (gitignore syntax) controls what gets excluded. Teams commit `graphify-out/` to git for shared graph access. Git merge drivers handle `graph.json` conflict resolution.

## Privacy Model

Code stays local (tree-sitter, no network). Documents and media go to the chosen LLM backend. Full local operation possible via Ollama backend.

## Installation

Requires Python 3.10+ and `uv` (or `pipx`). Optional extras cover PDF, Office, Google Sheets, video transcription, Neo4j, and per-LLM backends.

## ⚠ Code Graph vs Claude's Native Agentic Search (Important)

Claude Code's default search is "agentic search" — grep, glob, and file reads chained together. For many coding tasks this live-search approach outperforms classic RAG because it reads exactly what's needed.

**Small project — grep is often fine:**
1. Search keyword
2. Read 2–5 files
3. Fix issue

A code graph adds overhead here with no benefit.

**Large project — grep gets expensive:**
1. Search returns many files
2. Claude reads too much
3. Tool output grows large
4. Token cost spikes

That's where graph/RAG tools can help — *only if* they narrow the file set before Claude reads files. If the graph doesn't reduce what Claude has to read, it adds cost rather than saving it (confirmed by real tests below).

**Practical rule:**
- Small repo / known files → grep is fine
- Large repo / unknown flow → use code graph narrowly

## ⚠ When to Use (Important)

Graph/code tools become useful only when the project is large or unfamiliar:

| Project size | Familiarity | Decision |
|---|---|---|
| Small (<100 files) | You know the files | **Do not use Graphify** — no benefit |
| Large (100+ files, multiple modules) | Unknown structure | **Use Graphify narrowly** |
| Huge repo with cross-file dependencies | Unknown | **Graphify can save significant time** |

Threshold signals: 100+ files, multiple modules, cross-file dependencies you don't already understand.

## ⚠ Token Cost: Real Test Results (Important)

Tested against [PilotDataPlatform/blueprint](https://github.com/PilotDataPlatform/blueprint) — a mid-to-large repo. Question: *"How does the drop pipeline state machine work?"*

**With Graphify:**
```
Total cost:            $0.3136
Total duration (API):  58s / wall: 2m 30s
claude-haiku-4-5:   443 input, 14 output, 0 cache read, 0 cache write       ($0.0005)
claude-sonnet-4-6:  378 input, 2.6k output, 512.3k cache read, 31.6k write  ($0.3131)
```

**Without Graphify:**
```
Total cost:            $0.3177
Total duration (API):  1m 17s / wall: 1m 28s
claude-haiku-4-5:   504 input, 4.5k output, 993.7k cache read, 91.6k write  ($0.2370)
claude-sonnet-4-6:  347 input, 893 output, 72.0k cache read, 11.9k write    ($0.0807)
```

**Finding:** Virtually no cost difference ($0.3136 vs $0.3177).

Tested 4 questions total on the same repo — all yielded similar results:

| Question | Result |
|---|---|
| "How does the drop pipeline state machine work?" | No significant difference (numbers above) |
| "What RBAC checks happen before a route handler runs?" | Similar cost both ways |
| "What's the relationship between Collection, Zone, and GCS?" | Similar cost both ways |
| "How does session state get loaded?" | Similar cost both ways |

Graphify did not reduce tokens on this mid-size repo across any question type — structural, state machine, relationship, or auth flow. The benefit likely requires a truly huge repo where cross-file dependency mapping would otherwise cost many exploration round-trips.

**Conclusion:** Graphify's ROI threshold is higher than expected. Reserve for repos where manual exploration cost is clearly prohibitive.

## See Also

- [AI Output Control Strategies](ai-output-control-strategies.md)
