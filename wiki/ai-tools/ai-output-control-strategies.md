# AI Output Control Strategies

> Sources: gwang, 2026-06-01
> Raw: [2026-06-01-ai-output-control-strategies](../../raw/ai-tools/2026-06-01-ai-output-control-strategies.md)
> Updated: 2026-06-01

## Overview

Two strategies to cut AI verbosity: install the caveman output style plugin (persistent, no config needed) or use an inline terse directive (per-session, zero setup). Both reduce token waste and improve signal quality.

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

## See Also

- [Caveman Token Compression Plugin](caveman-token-compression.md) — skill-based output compression, ~65% token reduction
