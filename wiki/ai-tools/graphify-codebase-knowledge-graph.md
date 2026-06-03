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

Tested against [PilotDataPlatform/blueprint](https://github.com/PilotDataPlatform/blueprint) — a mid-to-large Django/React repo. 10 questions spanning structural, relationship, flow-tracing, and text-search types.

### Results

| # | Question | Graphify | No Graphify | Delta |
|---|----------|----------|-------------|-------|
| 1 | Trace full stack: frontend API call → database | $0.289 | $0.245 | +18% |
| 2 | Most central / most-depended-on modules | $0.092 | $0.252 | **−63%** |
| 3 | How does multi-tenancy work? | $0.150 | $0.138 | +9% |
| 4 | What files depend on the user auth model? | $0.104 | $0.229 | **−55%** |
| 5 | What happens on backend when user logs in? | $0.132 | $0.133 | ≈0% |
| 6 | Full data flow: user submits blueprint (complex) | $0.466 | $0.323 | +44% (quality worse) |
| 7 | What does API return when user has no permissions? | $0.107 | $0.086 | +24% |
| 8 | Are there any TODO comments about billing? | $0.060 | $0.050 | +21% |
| 9 | Which endpoints are missing auth checks? | $0.520 | $0.387 | +34% |
| 10 | What does function `apiFetch` return? | $0.112 | $0.090 | +24% |

### Pattern

**Graphify wins on graph-native questions** — those answerable purely from structural edges:
- Q2 (centrality ranking): 63% cheaper — graph directly encodes dependency counts
- Q4 (reverse dependency lookup): 55% cheaper — graph inverts the edge index instantly

**Graphify loses or breaks even everywhere else:**
- Semantic/body questions (Q7, Q10): graph can't read function logic, falls back to file reads anyway
- Text search (Q8): grep is strictly faster and cheaper
- Security audits (Q9): needs full body reads across many files — graph adds overhead without narrowing the read set
- Complex cross-layer flows (Q6): Graphify was more expensive *and* produced a lower-quality answer than native agentic search

### When to Use

| Question type | Use Graphify? |
|---|---|
| "What depends on X?" / reverse dependency | Yes — graph wins |
| "What are the most central modules?" | Yes — graph wins |
| "Trace flow across layers" (simple) | Neutral / slight loss |
| "Trace flow across layers" (complex, 6+ hops) | No — quality degrades |
| "What does function X return?" | No — grep + read is cheaper |
| "Are there TODOs about X?" | No — grep wins |
| Security audit / body-logic questions | No — needs full file reads |

**Practical rule:** Use Graphify only for questions whose answer is *in the graph structure itself* — dependency counts, reverse lookups, centrality. For anything that requires reading function bodies, skip it.

**Caveats:**
- Cold answer (no Graphify) reads actual files — may be more accurate for logic questions
- Graph quality depends on indexing quality; stale graphs skew results
- Token delta includes system prompt overhead — savings are proportionally smaller in long sessions

## See Also

- [AI Output Control Strategies](ai-output-control-strategies.md)
