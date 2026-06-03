# Claude Code: Model Selection by Task

> Sources: Personal testing and experience
> Raw: N/A — original notes

## Overview

Claude Code supports three model tiers — Haiku, Sonnet, Opus. Default is Sonnet. Right model for the task cuts cost without sacrificing quality. Wrong model either wastes money (Opus for trivial tasks) or loops and fails (Haiku for hard reasoning).

## Model Tiers

| Model | Strength | When to reach for it |
|-------|----------|----------------------|
| **Haiku** | Fast, cheap | Simple summaries, short lookups, high-volume cheap tasks |
| **Sonnet** | Best balance | Default for almost all real dev work |
| **Opus** | Deep reasoning | Architecture, hard bugs, large unfamiliar codebases |

## Task → Model Decision Table

| Task | Model | Reasoning |
|------|-------|-----------|
| Git commit message / diff summary | Haiku | Cheap, fast, enough for simple summaries |
| Explain code / simple config issue | Haiku or Sonnet | Haiku if cost matters, Sonnet if accuracy matters |
| Normal coding work | Sonnet | Best default balance of quality, speed, cost |
| Spring Boot bug fix | Sonnet | Strong enough for most real dev tasks |
| Write tests (MockMvc / JUnit) | Sonnet | Good quality without Opus cost |
| Docker / Nginx / OAuth config debugging | Sonnet | Usually enough, especially if logs are clear |
| Multi-file refactor | Sonnet first, Opus if stuck | Start cheaper, escalate only if needed |
| Architecture design | Opus | Better reasoning and tradeoff analysis |
| Hard production bug | Opus | Use when Sonnet loops or misses root cause |
| Large unfamiliar repo | Opus or Sonnet + code graph | Opus helps, but narrow file reads matter more |
| "Just do it, no explanation" task | Sonnet + terse prompt | Saves output tokens |
| Learning / explanation | Sonnet | Clear enough without overpaying |

## Practical Rules

**Default to Sonnet.** It handles the majority of real dev work at acceptable cost.

**Escalate to Opus only when:**
- Sonnet loops on the same wrong fix multiple times
- Sonnet misses the root cause after 2+ attempts
- Task requires weighing architectural tradeoffs across many files
- Codebase is large and unfamiliar, and you're exploring structure

**Drop to Haiku for:**
- High-volume, repetitive tasks (batch commit messages, doc summaries)
- Tasks where speed matters more than depth
- Cheap experimentation before committing to a full session

**Output token savings:** Pair terse prompts ("no explanation, just the fix") with Sonnet for tasks where you don't need reasoning output. Saves tokens proportional to output length.

## See Also

- [AI Output Control Strategies](ai-output-control-strategies.md)
- [CLAUDE.md Behavioral Guidelines](claude-md-behavioral-guidelines.md)
