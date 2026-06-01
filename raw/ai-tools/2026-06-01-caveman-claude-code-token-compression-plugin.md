# Caveman — Claude Code Token Compression Plugin

> Source: https://github.com/JuliusBrussee/caveman
> Collected: 2026-06-01
> Published: 2026-04-04

## Repository

- Author: Julius Brussee
- Stars: 67,328 (as of 2026-06-01)
- License: MIT
- Homepage: https://getcaveman.dev/
- Language: JavaScript

## Description

A Claude Code skill/plugin (also Codex, Gemini, Cursor, Windsurf, Cline, Copilot, 30+ more) that makes the agent talk like a caveman — cuts ~75% of output tokens, keeps full technical accuracy. Brain still big. Mouth small.

## Before / After Example

Normal Claude (69 tokens):
> "The reason your React component is re-rendering is likely because you're creating a new object reference on each render cycle. When you pass an inline object as a prop, React's shallow comparison sees it as a different object every time, which triggers a re-render. I'd recommend using useMemo to memoize the object."

Caveman Claude (19 tokens):
> "New object ref each render. Inline object prop = new ref = re-render. Wrap in `useMemo`."

## Install

```bash
# macOS / Linux / WSL / Git Bash
curl -fsSL https://raw.githubusercontent.com/JuliusBrussee/caveman/main/install.sh | bash

# Windows (PowerShell 5.1+)
irm https://raw.githubusercontent.com/JuliusBrussee/caveman/main/install.ps1 | iex
```

Requires Node ≥18. ~30 seconds. Safe to re-run.

**Trigger:** `/caveman` or "talk like caveman". Stop: "normal mode".

## Skills

| Skill | What |
|---|---|
| `/caveman [lite\|full\|ultra\|wenyan]` | Compress every reply. Levels stick until session end. |
| `/caveman-commit` | Conventional Commit messages, ≤50 char subject. |
| `/caveman-review` | One-line PR comments: `L42: 🔴 bug: user null. Add guard.` |
| `/caveman-stats` | Real session token usage + lifetime savings + USD. |
| `/caveman-compress <file>` | Rewrite memory file into caveman-speak. Cuts ~46% input tokens per session. |
| `caveman-shrink` | MCP middleware. Wraps any MCP server, compresses tool descriptions. |
| `cavecrew-*` | Caveman subagents (investigator/builder/reviewer). ~60% fewer tokens than vanilla. |

## Compression Levels

- `lite` — drop filler words only
- `full` — default caveman (fragments OK, no articles/pleasantries)
- `ultra` — telegraphic
- `wenyan` — classical Chinese, even shorter

## Benchmarks (Real Claude API Token Counts)

Average **65% output reduction** across 10 prompts (range 22–87%).

| Task | Normal | Caveman | Saved |
|------|-------:|--------:|------:|
| Explain React re-render bug | 1180 | 159 | 87% |
| Fix auth middleware token expiry | 704 | 121 | 83% |
| Set up PostgreSQL connection pool | 2347 | 380 | 84% |
| Explain git rebase vs merge | 702 | 292 | 58% |
| Refactor callback to async/await | 387 | 301 | 22% |
| Architecture: microservices vs monolith | 446 | 310 | 30% |
| Review PR for security issues | 678 | 398 | 41% |
| Docker multi-stage build | 1042 | 290 | 72% |
| Debug PostgreSQL race condition | 1200 | 232 | 81% |
| Implement React error boundary | 3454 | 456 | 87% |
| **Average** | **1214** | **294** | **65%** |

Eval harness: three-arm (baseline / terse / skill) — caveman compared against "Answer concisely." not verbose default, so delta is honest.

## caveman-compress Benchmarks (Memory Files)

| File | Original | Compressed | Saved |
|---|---:|---:|---:|
| claude-md-preferences.md | 706 | 285 | 59.6% |
| project-notes.md | 1145 | 535 | 53.3% |
| claude-md-project.md | 1122 | 636 | 43.3% |
| todo-list.md | 627 | 388 | 38.1% |
| mixed-with-code.md | 888 | 560 | 36.9% |
| **Average** | **898** | **481** | **46%** |

## User Test Data (gwang, 2026-06-01)

Caveman OFF session:
- claude-haiku-4-5: 538 input, 4.5k output, 464.2k cache read, 37.6k cache write ($0.1162)
- claude-sonnet-4-6: 696 input, 937 output, 160.2k cache read, 12.2k cache write ($0.1100)

Caveman ON session:
- Total cost: $0.3025
- claude-haiku-4-5: 567 input, 7.6k output, 603.6k cache read, 63.1k cache write ($0.1778)
- claude-sonnet-4-6: 354 input, 1.7k output, 156.8k cache read, 13.5k cache write

Observation: sonnet output tokens dropped from 937 → 1.7k (note: this session had more activity overall; total cost was higher due to more cache writes, not because caveman costs more). Sonnet input dropped 696 → 354 (output token reduction per-response was significant). Quality tradeoff noted — user should monitor output quality.

## How It Works

1. Install drops skill file in agent.
2. Skill tells agent: drop filler, keep substance, use fragments.
3. For Claude Code, hook writes tiny flag file each session — agent sees flag, talks caveman from message one.
4. Stats command reads Claude Code session log, counts tokens saved, writes to statusline.
5. caveman-compress rewrites memory files (CLAUDE.md, project notes) so each session starts with smaller context.

## Important Note on Scope

> Caveman only affects output tokens — thinking/reasoning tokens untouched. Caveman no make brain smaller. Caveman make *mouth* smaller.

A March 2026 paper "Brevity Constraints Reverse Performance Hierarchies in Language Models" (arxiv 2604.00025) found constraining large models to brief responses improved accuracy by 26 points on certain benchmarks.

## Ecosystem

| Repo | What |
|------|------|
| caveman (this) | Output compression |
| caveman-code | Whole terminal coding agent |
| cavemem | Cross-agent memory |
| cavekit | Spec-driven build loop |
| cavegemma | Gemma 4 31B fine-tuned on caveman pairs |

## Agent Support (30+)

Auto-activates: Claude Code, Gemini CLI, opencode, OpenClaw, Codex CLI
Per-session `/caveman`: Cursor, Windsurf, Cline, Copilot, Continue, Kilo Code, Roo Code, Augment Code, Aider Desk, Amp, and 20+ more.
