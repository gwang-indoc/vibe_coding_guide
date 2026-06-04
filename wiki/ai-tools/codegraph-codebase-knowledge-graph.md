# CodeGraph: Codebase Knowledge Graph Tool

> Sources: colbymchenry/codegraph (GitHub), 2026-06-03
> Raw: [CodeGraph — Codebase Knowledge Graph](../../raw/ai-tools/2026-06-03-codegraph-codebase-knowledge-graph.md)

## Overview

CodeGraph (TypeScript, MIT, 39.3k stars) is a pre-indexed code knowledge graph that integrates via MCP server into Claude Code, Cursor, Codex, Gemini CLI, and others. It extracts symbol relationships from source files using tree-sitter AST parsing, stores them in a local SQLite database, and exposes them as MCP tools. No LLM needed for indexing — pure static analysis, 100% local. Claimed benchmark: 16% cheaper, 58% fewer tool calls, 22% faster across 7 real-world codebases.

**Contrast with Graphify:** CodeGraph is pure static analysis (no LLM backend for indexing), MCP-native, TypeScript, stores in SQLite. Graphify is Python, uses LLM for doc/multimedia extraction, outputs HTML/JSON/Markdown, and integrates via slash commands. See [Graphify: Codebase Knowledge Graph Tool](graphify-codebase-knowledge-graph.md) for comparison.

## How It Works

1. **Extraction** — tree-sitter parses source files into ASTs; language-specific queries extract nodes (functions, classes, methods) and edges (calls, imports, extends, implements)
2. **Storage** — local SQLite at `.codegraph/codegraph.db` with FTS5 full-text search; no data leaves the machine
3. **Resolution** — function calls resolved to definitions, imports to source files, class inheritance, framework routing patterns
4. **Auto-sync** — MCP server watches via native OS file events (FSEvents / inotify / ReadDirectoryChangesW), debounced 2-second quiet window; incremental sync keeps graph fresh as you code

## Installation

```bash
# macOS / Linux
curl -fsSL https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.sh | sh

# Windows
irm https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.ps1 | iex

# npm (any platform)
npm i -g @colbymchenry/codegraph
```

After install, open a new terminal:

```bash
codegraph install        # wire into detected agents (Claude Code, Cursor, Codex, etc.)
cd your-project
codegraph init -i        # init + build graph
```

No config file needed. Language detection is automatic by file extension.

## MCP Tools

| Tool | Use for |
|------|---------|
| `codegraph_explore` | **Primary.** Architecture questions, flow tracing, surveying an area — returns verbatim source grouped by file |
| `codegraph_search` | Locate a symbol by name |
| `codegraph_callers` | What calls this function? |
| `codegraph_callees` | What does this function call? |
| `codegraph_impact` | What breaks if I change this symbol? |
| `codegraph_node` | Full source for one specific symbol |
| `codegraph_files` | Indexed file structure |
| `codegraph_status` | Index health and stats |

The MCP server delivers usage guidance to the agent automatically in the `initialize` response — nothing added to `CLAUDE.md`.

## Key CLI Commands

```bash
codegraph install                 # wire into agents
codegraph uninstall               # remove from agents
codegraph init -i                 # init + index project
codegraph sync                    # incremental update
codegraph status                  # index health
codegraph callers <symbol>        # reverse lookup
codegraph impact <symbol>         # blast radius analysis
codegraph affected [files...]     # which tests are affected by changed files
codegraph serve --mcp             # start MCP server manually
```

`codegraph affected` is useful in CI: `git diff --name-only | codegraph affected --stdin --quiet` pipes changed files and returns affected test files.

## Benchmark Results

Tested across 7 real-world open-source codebases, Claude Opus 4.8 headless, median of 4 runs, architecture-type questions only.

> Average: **16% cheaper · 47% fewer tokens · 22% faster · 58% fewer tool calls**

| Codebase | Size | Cost | Tokens | Time | Tool calls |
|----------|------|------|--------|------|------------|
| VS Code | ~10k TS files | −18% | −64% | −11% | −81% |
| Django | ~3k Python | −8% | −60% | −13% | −77% |
| OkHttp | ~645 Java | −25% | −54% | −31% | −50% |
| Alamofire | ~110 Swift | −40% | −64% | −33% | −58% |
| Gin | ~110 Go | −19% | −23% | −24% | −44% |
| Tokio | ~790 Rust | even | −38% | −18% | −57% |
| Excalidraw | ~640 TS | even | −25% | −27% | −40% |

Mechanism: with CodeGraph, the agent answers via `codegraph_explore` with near-zero file reads. Without it, the agent spends budget on grep/find/Read discovery loops. Cost savings are largest on small/medium repos (Alamofire, OkHttp); break-even on response-heavy ones (Excalidraw, Tokio) where CodeGraph trades many small reads for fewer large cache-heavy responses.

**Caveat:** All 7 benchmark queries are architecture/flow questions — the category where graph tools are strongest. Performance on body-logic, text search, or security audit questions is not benchmarked.

### Independent Test Results (mid-size Django/React repo)

Tested against [PilotDataPlatform/blueprint](https://github.com/PilotDataPlatform/blueprint) — mid-size Django/React repo. 4 question types across structural, flow, and security categories. Claude Sonnet 4.6.

| # | Question | Without CodeGraph | With CodeGraph | Delta |
|---|----------|-------------------|----------------|-------|
| 1 | Explain backend → frontend API routing | $0.103 | $0.157 | +52% |
| 2 | What happens on backend when user logs in? | $0.268 | $0.326 | +22% |
| 3 | Trace full stack: frontend API call → database | $0.287 | $0.181 | **−37%** |
| 4 | Which endpoints are missing auth checks? | $0.281 | $0.373 | +33% |

**Pattern:** CodeGraph saved cost only on Q3 — a pure structural traversal question. Lost on body-logic (Q1, Q2) and security audit (Q4) where the agent still had to read file contents regardless.

**Conclusion:** On a mid-size repo, CodeGraph overhead exceeds benefit for most question types. Q3 win (−37%) matches the vendor benchmark category (architecture/flow traversal). The vendor benchmarks appear to be optimized for that exact question type. **ROI threshold is likely high — reserve for very large repos or specifically for structural/dependency traversal questions.**

This matches the Graphify pattern: graph tools win narrowly on structural queries, lose on anything requiring body-level code reading. See [Graphify test results](graphify-codebase-knowledge-graph.md#-token-cost-real-test-results-important) for comparison.

## ⚠ When to Use

| Question type | CodeGraph helps? | Evidence |
|---|---|---|
| "How does X reach Y?" / full stack trace | Yes | −37% in independent test, −60% in vendor bench |
| "What depends on X?" / reverse lookup | Likely yes | not tested independently |
| Architecture / centrality questions | Likely yes | vendor bench only |
| "What does function X return?" — body logic | No | +22–52% in independent tests |
| "Which endpoints missing auth?" — security audit | No | +33% in independent test |
| "Any TODO comments about X?" — text search | No | grep wins (not tested) |

**Repo size matters:** Vendor benchmarks cover repos from ~110 to ~10k files and show wins across all sizes — but those are all architecture questions. Independent testing on a mid-size repo with mixed question types shows CodeGraph adds cost 3 out of 4 times. Until tested on very large repos with the same mixed question types, treat vendor claims skeptically for non-architecture queries.

## Features

- **Framework-aware routes** — 14 frameworks (Django, Flask, FastAPI, Express, NestJS, Laravel, Spring, Rails, Gin, Axum, Axum, ASP.NET, Vapor, React Router, SvelteKit)
- **Cross-language bridging** — Swift ↔ ObjC, React Native legacy bridge + TurboModules + Fabric, Expo Modules, RN event channels
- **20+ languages** — TypeScript, JavaScript, Python, Go, Rust, Java, C#, PHP, Ruby, C, C++, Swift, Kotlin, Scala, Dart, Svelte, Vue, Lua, Luau, and more
- **Embeddable as library** — `import CodeGraph from '@colbymchenry/codegraph'` for embedding in Electron or other Node apps (requires Node 22.5+)
- **CI integration** — `codegraph affected` for targeted test runs based on changed files

## Configuration

Zero-config. Skips `node_modules`, `vendor`, `dist`, `build`, `target`, `.venv`, `Pods`, `.next`, `.gitignore`d paths, and files >1 MB automatically. Override exclusions via `.gitignore` negation (`!vendor/`).

## vs Graphify

| Dimension | CodeGraph | Graphify |
|---|---|---|
| Language | TypeScript | Python |
| Stars | 39.3k | 58.8k |
| Indexing | Pure AST (tree-sitter), no LLM | AST + LLM for docs/multimedia |
| Storage | SQLite | JSON + HTML + Markdown |
| Integration | MCP server | Slash command / skill |
| Privacy | 100% local, no API keys | Code local; docs go to LLM backend |
| Config | Zero-config | `.graphifyignore`, team git commits |
| Benchmark evidence | 7 codebases, architecture Qs | 10 Qs on 1 mid-size Django/React repo |
| Multimedia support | No | Yes (PDFs, images, video) |

Independent test on blueprint repo (mid-size Django/React, 10 question types): Graphify showed savings only on pure structural queries (−63% centrality, −55% reverse lookup); added cost on body-logic and text search questions. CodeGraph's published benchmarks only cover architecture/flow questions, which aligns with the query types where graph tools win.

## See Also

- [Graphify: Codebase Knowledge Graph Tool](graphify-codebase-knowledge-graph.md)
- [AI Output Control Strategies](ai-output-control-strategies.md)
