# CodeGraph — Codebase Knowledge Graph for AI Coding Agents

> Source: https://github.com/colbymchenry/codegraph
> Collected: 2026-06-03
> Published: Unknown

Pre-indexed code knowledge graph for Claude Code, Codex, Gemini, Cursor, OpenCode, AntiGravity, Kiro, and Hermes Agent — fewer tokens, fewer tool calls, 100% local.

**~16% cheaper · ~58% fewer tool calls · 100% local**

TypeScript · MIT · 39,276 stars (as of 2026-06-03)

## Get Started

### 1. Install the CLI

**No Node.js required** — one command grabs the right build for your OS:

```bash
# macOS / Linux
curl -fsSL https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.sh | sh

# Windows (PowerShell)
irm https://raw.githubusercontent.com/colbymchenry/codegraph/main/install.ps1 | iex
```

Already have Node? Use npm instead (works on any version):

```bash
npm i -g @colbymchenry/codegraph
```

### 2. Wire up your agent(s)

In a **new terminal**, run the installer to connect CodeGraph to the agents you use:

```bash
codegraph install
```

Detects and auto-configures Claude Code, Cursor, Codex CLI, opencode, Hermes Agent, Gemini CLI, Antigravity IDE, and Kiro — wiring the CodeGraph MCP server into each. (Shortcut: `npx @colbymchenry/codegraph` downloads and runs this in one go.)

### 3. Initialize each project

```bash
cd your-project
codegraph init -i
```

`codegraph init` creates the local `.codegraph/` index directory; `-i` (`--index`) also builds the initial graph in the same step.

### Uninstall

```bash
codegraph uninstall
```

Reverses the installer — strips CodeGraph's MCP server config, instructions, and permissions from each configured agent.

---

## Why CodeGraph?

When Claude Code explores a codebase, it spawns **Explore agents** that scan files with grep, glob, and Read — consuming tokens on every tool call.

**CodeGraph gives those agents a pre-indexed knowledge graph** — symbol relationships, call graphs, and code structure. Agents query the graph instantly instead of scanning files.

### Benchmark Results

Tested across **7 real-world open-source codebases** spanning 7 languages, comparing an agent (Claude Code, headless) answering one architecture question **with** and **without** CodeGraph. Each cell is the savings at the **median of 4 runs per arm**. Re-validated on Opus 4.8 (2026-06-02).

> **Average: 16% cheaper · 47% fewer tokens · 22% faster · 58% fewer tool calls**

| Codebase | Language | Cost | Tokens | Time | Tool calls |
|----------|----------|------|--------|------|------------|
| **VS Code** | TypeScript · ~10k files | 18% cheaper | 64% fewer | 11% faster | 81% fewer |
| **Excalidraw** | TypeScript · ~640 | even | 25% fewer | 27% faster | 40% fewer |
| **Django** | Python · ~3k | 8% cheaper | 60% fewer | 13% faster | 77% fewer |
| **Tokio** | Rust · ~790 | even | 38% fewer | 18% faster | 57% fewer |
| **OkHttp** | Java · ~645 | 25% cheaper | 54% fewer | 31% faster | 50% fewer |
| **Gin** | Go · ~110 | 19% cheaper | 23% fewer | 24% faster | 44% fewer |
| **Alamofire** | Swift · ~110 | 40% cheaper | 64% fewer | 33% faster | 58% fewer |

**Methodology.** Each arm is `claude -p` (Claude Opus 4.8) run headlessly. Same question per repo, 4 runs per arm, median reported. Both arms have Read/Grep/Bash available. WITH = CodeGraph MCP server enabled; WITHOUT = empty MCP config.

**Queries used:**
- VS Code: "How does the extension host communicate with the main process?"
- Excalidraw: "How does Excalidraw render and update canvas elements?"
- Django: "How does Django's ORM build and execute a query from a QuerySet?"
- Tokio: "How does tokio schedule and run async tasks on its runtime?"
- OkHttp: "How does OkHttp process a request through its interceptor chain?"
- Gin: "How does gin route requests through its middleware chain?"
- Alamofire: "How does Alamofire build, send, and validate a request?"

---

## Key Features

| | |
|---|---|
| **Smart Context Building** | One tool call returns entry points, related symbols, and code snippets — no expensive exploration agents |
| **Full-Text Search** | Find code by name instantly across your entire codebase, powered by FTS5 |
| **Impact Analysis** | Trace callers, callees, and the full impact radius of any symbol before making changes |
| **Always Fresh** | File watcher uses native OS events (FSEvents/inotify/ReadDirectoryChangesW) with debounced auto-sync |
| **20+ Languages** | TypeScript, JavaScript, Python, Go, Rust, Java, C#, PHP, Ruby, C, C++, Objective-C, Swift, Kotlin, Dart, Lua, Luau, Svelte, Vue, Liquid, Pascal/Delphi |
| **Framework-aware Routes** | Recognizes web-framework routing files and links URL patterns to handlers across 14 frameworks |
| **Mixed iOS / React Native / Expo** | Bridges cross-language flows: Swift ↔ ObjC bridging, RN bridge, Expo Modules |
| **100% Local** | No data leaves your machine. No API keys. No external services. SQLite database only |

---

## How It Works

1. **Extraction** — tree-sitter parses source code into ASTs. Language-specific queries extract nodes (functions, classes, methods) and edges (calls, imports, extends, implements).
2. **Storage** — Everything goes into a local SQLite database (`.codegraph/codegraph.db`) with FTS5 full-text search.
3. **Resolution** — After extraction, references are resolved: function calls → definitions, imports → source files, class inheritance, framework-specific patterns.
4. **Auto-Sync** — MCP server watches the project using native OS file events. Changes debounced (2-second quiet window), incrementally synced.

---

## MCP Tools

| Tool | Purpose |
|------|---------|
| `codegraph_explore` | **Primary.** Answer almost any question in one call — "how does X work", a flow ("how does X reach Y"), or surveying an area |
| `codegraph_search` | Find symbols by name across the codebase |
| `codegraph_callers` | Find what calls a function |
| `codegraph_callees` | Find what a function calls |
| `codegraph_impact` | Analyze what code is affected by changing a symbol |
| `codegraph_node` | Get one specific symbol's details + full source |
| `codegraph_files` | Get indexed file structure (faster than filesystem scanning) |
| `codegraph_status` | Check index health and statistics |

---

## CLI Reference

```bash
codegraph install                 # Run installer
codegraph uninstall               # Remove from agents
codegraph init [path]             # Initialize in a project (--index to also index)
codegraph uninit [path]           # Remove from a project
codegraph index [path]            # Full index
codegraph sync [path]             # Incremental update
codegraph status [path]           # Show statistics
codegraph query <search>          # Search symbols
codegraph files [path]            # Show file structure
codegraph callers <symbol>        # Find what calls a function/method
codegraph callees <symbol>        # Find what a function/method calls
codegraph impact <symbol>         # Analyze impact of changing a symbol
codegraph affected [files...]     # Find test files affected by changes
codegraph serve --mcp             # Start MCP server
```

---

## Configuration

Zero-config. No config file. Language support is automatic from file extension.

What it skips out of the box: dependency/build/cache directories (`node_modules`, `vendor`, `dist`, `build`, `target`, `.venv`, `Pods`, `.next`), anything in `.gitignore`, files larger than 1 MB.

To exclude more: add to `.gitignore`. To pull a default-excluded dir back in: use a negation (`!vendor/`).

---

## Supported Languages

TypeScript, JavaScript, Python, Go, Rust, Java, C#, PHP, Ruby, C, C++, Objective-C (partial), Swift, Kotlin, Scala, Dart, Svelte, Vue, Liquid, Pascal/Delphi, Lua, Luau.

## Supported Platforms

Windows, macOS, Linux — x64 and arm64. Ships self-contained bundled Node runtime.

## License

MIT
