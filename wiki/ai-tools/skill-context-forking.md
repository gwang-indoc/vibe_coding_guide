# Claude Code: Skill Context Forking & Per-Step Agent Isolation

> Sources: User tip, 2026-06-15; Claude Code docs (skills.md, sub-agents.md, commands.md), verified 2026-06-15
> Raw: [Skill fork + per-step agent context isolation](../../raw/ai-tools/2026-06-15-skill-fork-context-isolation.md)

## Overview

A skill can run in an isolated context window so its work doesn't pollute (or get polluted by) the parent conversation. The mechanism is the `context: fork` frontmatter key — **not** `fork: true` as commonly misremembered. A skill can further reduce context bloat by instructing Claude to delegate each step to subagents, which run in their own contexts and return only results. Both are documented, supported patterns.

## `context: fork` frontmatter

- **Exact key:** `context: fork` (in SKILL.md frontmatter). `fork: true` is wrong and has no effect.
- **Behavior:** Runs the skill in a forked subagent context — an isolated context window with a fresh environment. It does not share the parent conversation history, previously invoked skills, or files Claude has already read.
- **Added:** Claude Code v2.1.0 (2026-01-07).
- **Use when:** the skill does heavy, self-contained work whose intermediate context (large file reads, exploration) you don't want spilling into the main conversation.

## Per-step subagent isolation

A second, complementary layer: the skill's own instructions tell Claude to use subagents (Agent/Task tool) for each step. Each subagent completes its task in its own context and returns only the result to the parent, which passes relevant context to the next step.

- Documented pattern (sub-agents.md): "For multi-step workflows, ask Claude to use subagents in sequence."
- Subagents can nest up to 5 levels deep (v2.1.172+).
- Stacks with `context: fork`: the skill runs forked, and within that fork each step is further isolated.

## Caveat: built-in `/code-review` is not user-configurable

`/code-review` is a real built-in **bundled** skill (reviews the current diff for correctness bugs plus reuse/simplification/efficiency cleanups; supports effort levels, `--fix`, `--comment`). However, bundled skills ship as fixed implementations — you **cannot** add `context: fork` to their frontmatter. The claim "add `fork:true` to the built-in `/code-review` to improve performance" does not work as stated. To get forked/isolated behavior, write a custom skill (or copy/wrap the review logic into one) and set `context: fork` there.

## See Also

- [Claude Code: Context Management Strategy](claude-code-context-management.md)
- [Claude Code: Dynamic Context Injection](claude-code-dynamic-context-injection.md)
