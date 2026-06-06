# Claude Code: Context Management Strategy

> Sources: User observation, 2026-06-06
> Raw: [Claude Code Context Management Strategy](../../raw/ai-tools/2026-06-06-claude-code-context-management-strategy.md)
> Updated: 2026-06-06

## Overview

Two-phase workflow: explore to build a plan, then clear context and execute from the plan. Strategic manual compaction gives finer control than auto-compact.

## Explore → Plan → Clear → Execute

Exploration accumulates context (file reads, search results, dead ends) that has no value once a plan exists. Plan mode in Claude Code defaults to clearing context after plan approval — use this.

**Pattern:**
1. Explore freely. Let context grow.
2. Lock in a plan (plan mode).
3. Clear context (default on plan approval).
4. Execute from the plan alone — no stale exploration noise.

This keeps execution context tight and focused.

## Strategic Compacting

Use `/compact`. Compact at phase boundaries, not mid-task:

- Research → Planning: YES
- Planning → Implementation: YES
- Mid-implementation: NO
- After failed approach: YES

## See Also

- [CLAUDE.md Behavioral Guidelines](claude-md-behavioral-guidelines.md)
- [Claude Code: Model Selection by Task](claude-code-model-selection.md)
