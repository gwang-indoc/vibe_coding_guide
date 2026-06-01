# Caveman Token Compression Plugin

> Sources: Julius Brussee, 2026-04-04
> Raw: [2026-06-01-caveman-claude-code-token-compression-plugin](../../raw/ai-tools/2026-06-01-caveman-claude-code-token-compression-plugin.md)

## ⚠️ When NOT to Use Caveman

**Do not use caveman for any task that produces deliverable text output:**

- Writing specs, PRDs, design docs, RFCs, ADRs
- Generating README, wiki articles, or documentation
- Drafting emails, Slack messages, or user-facing copy
- Any output a human will read directly

**Good fits:** debugging, code review, refactoring, Q&A, codebase exploration, explaining internal mechanics. Caveman excels at **exploring an unfamiliar codebase** — compressed Q&A costs far fewer tokens and the answers are still precise.

**Bad fits:** document generation tasks. Caveman-speak leaks into the artifact. The output quality problem is invisible in the diff but obvious to the reader.

> **Rule:** If the AI's words become the deliverable, disable caveman first (`normal mode`). If the AI's words are consumed only by you, caveman is fine.

---

## Overview

Caveman is a Claude Code skill (also Codex, Gemini, Cursor, 30+ agents) that compresses AI output by making the agent speak in caveman-style fragments — dropping articles, filler words, and pleasantries while preserving full technical accuracy. Benchmarked at ~65% output token reduction (range 22–87%). Only output tokens are affected; thinking/reasoning tokens and technical quality remain intact.

## How It Works

Install drops a skill file into the agent. For Claude Code, a session-start hook writes a flag file so caveman activates from message one — no per-session `/caveman` needed. The `caveman-compress` sub-skill rewrites memory files (CLAUDE.md, project notes) into caveman-speak, cutting ~46% of input tokens every session permanently.

## Compression Levels

| Level | Behavior |
|---|---|
| `lite` | Drop filler words only |
| `full` | Default — fragments OK, no articles/pleasantries/hedging |
| `ultra` | Telegraphic, maximum compression |
| `wenyan` | Classical Chinese, even shorter |

Switch with `/caveman [lite|full|ultra|wenyan]`. Level persists until session end. Stop: `"normal mode"`.

## Skills

| Command | What |
|---|---|
| `/caveman` | Activate compression (level optional) |
| `/caveman-commit` | Conventional Commit messages, ≤50 char subject, why over what |
| `/caveman-review` | One-line PR comments: `L42: 🔴 bug: user null. Add guard.` |
| `/caveman-stats` | Real token usage + lifetime savings + USD cost |
| `/caveman-compress <file>` | Rewrite memory/CLAUDE.md into caveman-speak (~46% input savings forever) |
| `caveman-shrink` | MCP middleware — wraps any MCP server, compresses tool descriptions |
| `cavecrew-*` | Caveman subagents (investigator/builder/reviewer), ~60% fewer tokens |

## Install

```bash
# macOS / Linux / WSL
curl -fsSL https://raw.githubusercontent.com/JuliusBrussee/caveman/main/install.sh | bash

# Windows PowerShell
irm https://raw.githubusercontent.com/JuliusBrussee/caveman/main/install.ps1 | iex
```

Requires Node ≥18. ~30 seconds. Auto-detects all installed agents. Safe to re-run.

For single agent: `claude plugin marketplace add JuliusBrussee/caveman && claude plugin install caveman@caveman`

## Benchmarks

**Output token reduction** (real Claude API, 10 prompts, three-arm eval vs. "Answer concisely." baseline):

| Task | Normal | Caveman | Saved |
|------|-------:|--------:|------:|
| React re-render bug | 1180 | 159 | 87% |
| Auth middleware fix | 704 | 121 | 83% |
| PostgreSQL connection pool | 2347 | 380 | 84% |
| git rebase vs merge | 702 | 292 | 58% |
| Refactor to async/await | 387 | 301 | 22% |
| Microservices vs monolith | 446 | 310 | 30% |
| PR security review | 678 | 398 | 41% |
| Docker multi-stage build | 1042 | 290 | 72% |
| PostgreSQL race condition | 1200 | 232 | 81% |
| React error boundary | 3454 | 456 | 87% |
| **Average** | **1214** | **294** | **65%** |

**caveman-compress on memory files** (avg 46% input token reduction):

| File | Original | Compressed | Saved |
|---|---:|---:|---:|
| claude-md-preferences.md | 706 | 285 | 59.6% |
| project-notes.md | 1145 | 535 | 53.3% |
| claude-md-project.md | 1122 | 636 | 43.3% |
| todo-list.md | 627 | 388 | 38.1% |
| mixed-with-code.md | 888 | 560 | 36.9% |
| **Average** | **898** | **481** | **46%** |

## Usage Note

Use caveman when you are already familiar with the project. Compressed output is harder to catch when wrong — monitor AI output quality carefully, especially on unfamiliar codebases.

## Key Insight

> Caveman only affects output tokens. Thinking/reasoning tokens untouched. Brain still big. Mouth small.

A March 2026 paper (arxiv 2604.00025) found brevity constraints on large models **improved accuracy by 26 points** on certain benchmarks — verbose is not always better.

## Ecosystem

| Repo | What |
|------|------|
| caveman | Output compression (this article) |
| caveman-code | Full terminal coding agent, ~2× fewer tokens than Codex |
| cavemem | Cross-agent memory compression |
| cavekit | Spec-driven build loop |
| cavegemma | Gemma 4 31B fine-tuned on caveman pairs |

## See Also

- [AI Output Control Strategies](ai-output-control-strategies.md) — CLAUDE.md directives, skill files, and terse inline as alternatives/complements to caveman
