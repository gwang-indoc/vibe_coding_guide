# AI Output Control Strategies

> Source: user knowledge (gwang)
> Collected: 2026-06-01
> Published: Unknown

## Problem

AI models produce verbose output by default — explanations, summaries, preambles. This wastes tokens and slows reading.

## Strategy 1: CLAUDE.md Directive (persistent, project-wide)

Add to CLAUDE.md:

```
Be concise.
No explanations.
No summaries.
Only output required artifacts.
```

Effect: AI skips prose, outputs only what was asked. Saves tokens. Improves signal-to-noise.

## Strategy 2: Skill (persistent, behavior-encoded)

Teach the AI HOW you want it to work via a skill file. A skill encodes rules, patterns, and workflows — more powerful than a terse directive because it shapes behavior across many scenarios, not just output length.

Use when: you have a repeatable workflow (e.g. commit messages, code review style, wiki ingest).

## Strategy 3: Terse Directive (per-session, lightweight)

Tell the AI to be brief inline: "be brief", "terse", "one line", etc.

Simpler alternative to writing a skill. No file to maintain. Works per-session.

Use when: you want brevity for one conversation, not a permanent behavior change.

## Comparison

| Strategy | Scope | Effort | Best for |
|---|---|---|---|
| CLAUDE.md directive | Project-wide, every session | One-time edit | General output style |
| Skill file | Persistent, activatable | Moderate (write skill) | Repeatable workflows |
| Terse inline | Current session only | Zero | One-off brevity |
