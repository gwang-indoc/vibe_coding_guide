# Claude Code: Context Management Strategy

> Sources: User observation, 2026-06-06; User observation, 2026-06-10; User observation, 2026-06-19
> Raw: [Claude Code Context Management Strategy](../../raw/ai-tools/2026-06-06-claude-code-context-management-strategy.md); [Context Dumb Zone Thresholds](../../raw/ai-tools/2026-06-10-context-dumb-zone-thresholds.md); [Rewind Over Correction](../../raw/ai-tools/2026-06-10-rewind-over-correction.md); [Agent Milestone Compaction](../../raw/ai-tools/2026-06-19-agent-milestone-compaction.md)
> Updated: 2026-06-19

## Overview

Two-phase workflow: explore to build a plan, then clear context and execute from the plan. Strategic manual compaction gives finer control than auto-compact. Quality degrades noticeably around 40% context — manage thresholds proactively.

## Explore → Plan → Clear → Execute

Exploration accumulates context (file reads, search results, dead ends) that has no value once a plan exists. Plan mode in Claude Code defaults to clearing context after plan approval — use this.

**Pattern:**
1. Explore freely. Let context grow.
2. Lock in a plan (plan mode).
3. Clear context (default on plan approval).
4. Execute from the plan alone — no stale exploration noise.

This keeps execution context tight and focused.

## Context Degradation Thresholds ("Dumb Zone")

Quality degrades around **40%** context — described as "you hit this point where you have degrading results."

| Experience | Target | Ceiling |
|------------|--------|---------|
| Newcomer | < 40% | Wrap up at 60% |
| Experienced | < 30% (aggressive) | 60% only on simple tasks |

Use `/compact` or `/clear` when switching tasks to reset context.

## Rewind Over Correction

Failed attempts + in-context corrections pollute context — failed reasoning chains degrade future output.

**Rule:** don't correct in-place. Rewind to before the failure and re-prompt with what you learned.

| Action | How |
|--------|-----|
| Rewind one turn | Double-`Esc` |
| Rewind to specific point | `/rewind` |

Re-prompting after rewind incorporates the lesson without the noise.

## Strategic Compacting

Use `/compact`. Compact at phase boundaries, not mid-task:

- Research → Planning: YES
- Planning → Implementation: YES
- Mid-implementation: NO
- After failed approach: YES

### Agent Milestone Compaction

For multi-agent or long-running agent sessions, don't wait for the context limit — compact at named milestones:

| Milestone | Action |
|-----------|--------|
| After repo exploration | `/compact` |
| After implementation plan | `/compact` |
| After major code changes | `/compact` |
| After E2E test result | `/compact` |
| Before handoff to another agent | `/compact` |

**Threshold:** compact proactively when context usage hits **60–70%**. This overlaps with the "dumb zone" onset — acting early keeps agent quality high rather than recovering from degraded output.

## See Also

- [CLAUDE.md Behavioral Guidelines](claude-md-behavioral-guidelines.md)
- [Claude Code: Model Selection by Task](claude-code-model-selection.md)
- [Claude Code: Dynamic Context Injection](claude-code-dynamic-context-injection.md)
- [Claude Code: Skill Context Forking & Per-Step Agent Isolation](skill-context-forking.md)
- [Claude Code: Auto Mode + Parallel Sessions Workflow](auto-mode-parallel-workflow.md)
