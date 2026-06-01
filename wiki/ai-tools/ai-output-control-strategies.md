# AI Output Control Strategies

> Sources: gwang, 2026-06-01
> Raw: [2026-06-01-ai-output-control-strategies](../../raw/ai-tools/2026-06-01-ai-output-control-strategies.md)
> Updated: 2026-06-01

## Overview

Three strategies to cut AI verbosity: a persistent CLAUDE.md directive (project-wide), a skill file (behavior-encoded workflows), or an inline terse directive (per-session). All reduce token waste and improve signal quality.

## Strategy 1: CLAUDE.md Directive

Add to `CLAUDE.md` for project-wide effect every session:

```
Be concise.
No explanations.
No summaries.
Only output required artifacts.
```

AI skips preambles and prose — outputs only what was asked. Zero per-session cost. Best for general output style control.

**Example — Java project CLAUDE.md:**

```markdown
# Coding Rules

Be concise.

No summaries.

No explanations unless requested.

Output code and tests only.

For Java:
- Use constructor injection
- Use JUnit 5
- Use AssertJ
- Use @WebMvcTest for controller tests
- Follow TDD
```

Combines output style control with project-specific coding standards in one place. AI produces code + tests, no prose, and follows the stack rules every session.

## Strategy 2: Skill File

Encode HOW the AI should work in a skill file. More powerful than a terse directive: shapes behavior across many scenarios (commit messages, review style, wiki ingest), not just output length. Activatable on demand.

Use when: you have a repeatable workflow worth encoding once and reusing many times.

## Strategy 3: Terse Inline Directive

Tell the AI to be brief inline: `"be brief"`, `"terse"`, `"one line"`, `"no explanation"`.

No file to maintain. Zero setup. Works for one session.

Use when: you want brevity for one conversation, not permanent behavior change.

## Comparison

| Strategy | Scope | Setup effort | Best for |
|---|---|---|---|
| CLAUDE.md directive | Project-wide, every session | One-time file edit | General output style |
| Skill file | Persistent, activatable | Moderate (write skill) | Repeatable workflows |
| Terse inline | Current session only | None | One-off brevity |

## See Also

- [Caveman Token Compression Plugin](caveman-token-compression.md) — skill-based output compression, ~65% token reduction
