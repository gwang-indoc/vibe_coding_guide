# CLAUDE.md Behavioral Guidelines

> Sources: [multica-ai/andrej-karpathy-skills](https://github.com/multica-ai/andrej-karpathy-skills/blob/main/CLAUDE.md); User observation, 2026-06-10
> Raw: [CLAUDE.md Line Limit](../../raw/ai-tools/2026-06-10-claude-md-line-limit.md); [CLAUDE.md Important Tags](../../raw/ai-tools/2026-06-10-claude-md-important-tags.md)
> Updated: 2026-06-10

## Overview

Behavioral guidelines to reduce common LLM coding mistakes. Merge with project-specific CLAUDE.md as needed. Bias toward caution over speed — use judgment on trivial tasks.

## File Size

Target under **200 lines per file**. Split into multiple CLAUDE.md files (root + subdirectory) when approaching that limit rather than letting one file bloat.

## Conditional Importance Tags

As CLAUDE.md grows, Claude deprioritizes content further down the file. Wrap domain-specific rules in `<important if="...">` tags to prevent silent rule-skipping:

```xml
<important if="working with database migrations">
Never run DROP TABLE without an explicit user confirmation step.
</important>

<important if="editing API endpoints">
All responses must include the X-Request-ID header.
</important>
```

The `if` condition scopes when the rule is active — Claude applies it only in matching contexts, and the tag signals the rule should not be ignored.

## 1. Think Before Coding

Don't assume. Don't hide confusion. Surface tradeoffs.

Before implementing:
- State assumptions explicitly. If uncertain, ask.
- If multiple interpretations exist, present them — don't pick silently.
- If a simpler approach exists, say so. Push back when warranted.
- If something is unclear, stop. Name what's confusing. Ask.

## 2. Simplicity First

Minimum code that solves the problem. Nothing speculative.

- No features beyond what was asked.
- No abstractions for single-use code.
- No "flexibility" or "configurability" that wasn't requested.
- No error handling for impossible scenarios.
- If you write 200 lines and it could be 50, rewrite it.

> Ask: "Would a senior engineer say this is overcomplicated?" If yes, simplify.

## 3. Surgical Changes

Touch only what you must. Clean up only your own mess.

When editing existing code:
- Don't "improve" adjacent code, comments, or formatting.
- Don't refactor things that aren't broken.
- Match existing style, even if you'd do it differently.
- If you notice unrelated dead code, mention it — don't delete it.

When your changes create orphans:
- Remove imports/variables/functions that YOUR changes made unused.
- Don't remove pre-existing dead code unless asked.

> Test: every changed line should trace directly to the user's request.

## 4. Goal-Driven Execution

Define success criteria. Loop until verified.

Transform tasks into verifiable goals:
- "Add validation" → "Write tests for invalid inputs, then make them pass"
- "Fix the bug" → "Write a test that reproduces it, then make it pass"
- "Refactor X" → "Ensure tests pass before and after"

For multi-step tasks, state a brief plan:
```
1. [Step] → verify: [check]
2. [Step] → verify: [check]
3. [Step] → verify: [check]
```

Strong success criteria let you loop independently. Weak criteria ("make it work") require constant clarification.
