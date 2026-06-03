# Graphify — Codebase Knowledge Graph Tool

> Source: https://github.com/safishamsi/graphify
> Collected: 2026-06-03
> Published: Unknown

Graphify is an AI coding assistant skill that transforms project folders—containing code, SQL schemas, documentation, PDFs, images, and videos—into queryable knowledge graphs. The tool integrates with multiple AI coding assistants to provide semantic understanding of entire codebases.

## Core Features

**Graph Generation:**
- Converts any folder structure into an interactive knowledge graph
- Produces three output files: `graph.html` (visual), `GRAPH_REPORT.md` (analysis), and `graph.json` (queryable data)
- Identifies "god nodes" (most-connected concepts) and surprising connections between disparate modules

**Multi-Platform Support:**
Integrates with Claude Code, Codex, OpenCode, Kilo Code, Cursor, Gemini CLI, GitHub Copilot CLI, Aider, OpenClaw, Factory Droid, Trae, Hermes, Kimi Code, Amp, Kiro IDE, Pi coding agent, Devin CLI, and Google Antigravity.

**File Type Handling:**
- Code: 33 languages (Python, TypeScript, Rust, Go, Java, etc.)
- Docs: Markdown, HTML, RST, YAML
- Office: DOCX, XLSX
- Multimedia: PDFs, images, video/audio (with transcription)
- MCP configurations and Google Workspace files

**Advanced Capabilities:**
- PR dashboard with CI state, review status, and graph impact analysis
- Git hook automation for rebuilding on commits
- Cross-project global graph merging
- Obsidian vault and markdown wiki generation
- Neo4j export with Cypher queries
- MCP stdio server for structured access

## Technical Architecture

**Extraction Pipeline:**
Code is processed locally via tree-sitter AST (no external calls). Documentation and multimedia require LLM processing through configured backends (Claude, Gemini, OpenAI, DeepSeek, Kimi, Ollama, or AWS Bedrock).

**Key Technologies:**
- Tree-sitter for language-agnostic AST parsing
- Leiden algorithm for community detection
- Knowledge graph construction with semantic relationship inference
- Confidence scoring (`EXTRACTED`, `INFERRED`, `AMBIGUOUS` tags)

**Privacy Model:**
Code extraction happens locally. Docs/PDFs/images are sent to the configured LLM provider. Users control data residency via backend selection (Ollama runs fully locally).

## Installation

**Prerequisites:**
- Python 3.10+
- uv (recommended) or pipx

**Quick Start:**
```bash
uv tool install graphifyy
graphify install
/graphify .
```

**Optional Extras:**
PDF support, Office formats, Google Sheets, video transcription, Neo4j integration, Ollama local inference, and various LLM backends available via extras flags.

## Usage Examples

| Command | Purpose |
|---------|---------|
| `/graphify .` | Build graph for current folder |
| `/graphify . --update` | Re-extract only changed files |
| `/graphify query "what connects X to Y?"` | Semantic search |
| `graphify export callflow-html` | Generate Mermaid architecture diagrams |
| `graphify prs --triage` | AI-ranked PR review queue |

## Configuration

`.graphifyignore` files use `.gitignore`-style syntax to exclude paths. Team workflows commit `graphify-out/` to git for shared knowledge access. Git merge drivers prevent conflict markers in `graph.json`.

## Development

Uses uv for package management and pytest for testing. Active development on the `v8` branch. Contributors encouraged to add worked examples in `worked/{slug}/` with honest review documentation.

## Related Project

**Penpax** is a commercial always-on layer built on graphify that applies the same approach to meetings, browser history, emails, files, and code with on-device processing (free trial launching).

## Stats

- Stars: 58.8k | Forks: 6.1k
- License: MIT
- Primary Language: Python (100%)
