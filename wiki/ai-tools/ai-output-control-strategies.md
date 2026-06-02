# AI Output Control Strategies

> Sources: gwang, 2026-06-01
> Raw: [2026-06-01-ai-output-control-strategies](../../raw/ai-tools/2026-06-01-ai-output-control-strategies.md)
> Updated: 2026-06-01

## Overview

Two strategies to cut AI verbosity: install the caveman output style plugin (persistent, no config needed) or use an inline terse directive (per-session, zero setup). Both reduce token waste and improve signal quality. Know when NOT to use caveman before enabling it.

## Strategy 1: Caveman Output Style Plugin

Install [caveman-output-style-claude-code](https://github.com/carlosduplar/caveman-output-style-claude-code) — complete skill-based output style system for Claude Code. Handles output compression via hooks and skill files automatically. No CLAUDE.md edits or custom skill files needed.

Covers: compressed responses across all sessions, activatable modes, hook-based enforcement.

Use when: permanent output style control across all projects.

## Strategy 2: Terse Inline Directive

Tell AI to be brief inline: `"be brief"`, `"terse"`, `"one line"`, `"no explanation"`.

No setup. Works for one session only.

Use when: one-off brevity without installing anything.

## Comparison

| Strategy | Scope | Setup | Best for |
|---|---|---|---|
| Caveman plugin | Persistent, all sessions | Install repo | Permanent output control |
| Terse inline | Current session only | None | One-off brevity |

## When NOT to Use Caveman Output Style Plugin

**Do not use caveman for any task that produces deliverable text output:**

- Writing specs, PRDs, design docs, RFCs, ADRs
- Generating README, wiki articles, or documentation
- Drafting emails, Slack messages, or user-facing copy
- Any output a human will read directly

**Good fits:** debugging, code review, refactoring, Q&A, codebase exploration. Caveman excels at **exploring an unfamiliar codebase** — compressed Q&A costs far fewer tokens and answers stay precise.

**Bad fits:** document generation tasks. Caveman-speak leaks into the artifact. Output quality problem is invisible in the diff but obvious to the reader.

> **Rule:** If the AI's words become the deliverable, disable caveman first (`normal mode`). If consumed only by you, caveman is fine.
