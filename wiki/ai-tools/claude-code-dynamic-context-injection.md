# Claude Code: Dynamic Context Injection

> Sources: User observation, 2026-06-06; ECC (affaan-m), 2026-06-06
> Raw: [Claude Code Dynamic Context Injection](../../raw/ai-tools/2026-06-06-claude-code-dynamic-context-injection.md); [ECC: Harness-Native Operator System](../../raw/ai-tools/2026-06-06-ecc-harness-native-operator-system.md)
> Updated: 2026-06-06

## Overview

Don't load everything every session. CLAUDE.md and `.claude/rules/` are always-on — reserve them for truly universal rules. Use CLI flags, skills, and structured rules directories to inject context only when needed.

## Always-On vs On-Demand

| Mechanism | Scope | Loads |
|---|---|---|
| `~/.claude/CLAUDE.md` | User (global) | Every session |
| `.claude/CLAUDE.md` | Project | Every session |
| `.claude/rules/` | Project | Every session |
| CLI flags | Call-site | Only when passed |
| Skills | Call-site | Only when triggered |

**Problem with always-on:** context irrelevant to the current task wastes tokens and adds noise every session.

## CLI Flags for Dynamic Injection

Pass context only for sessions that need it:

```bash
# Inject a system prompt
claude --system-prompt "You are reviewing a migration. Focus on schema safety."

# Append to existing system prompt (preserves CLAUDE.md rules)
claude --append-system-prompt "Context: this file handles payment processing."

# Pass a file as context
claude --context @docs/api-spec.md "implement the /users endpoint"
```

## Structured Rules (ECC Pattern)

Instead of one monolithic CLAUDE.md, split rules by scope:

```
.claude/rules/
  common/        ← language-agnostic rules, always relevant
  typescript/    ← loaded only for TS work
  python/        ← loaded only for Python work
  security/      ← loaded only for security tasks
```

ECC uses this pattern across 6 language dirs. Claude Code v2.1+ auto-loads `hooks/hooks.json` from installed plugins — same principle applies to rules directories.

## Skills as On-Demand Context

Skills inject focused domain knowledge only when triggered — not every session. Each skill: one domain, under 800 lines, with explicit "When to Activate" criteria.

Contrast: CLAUDE.md rule = always loaded. Skill = loaded on demand.

## Token Optimization (ECC Defaults)

```
model: sonnet                      # default, escalate only when needed
MAX_THINKING_TOKENS: 10000         # cap extended thinking
CLAUDE_AUTOCOMPACT_PCT_OVERRIDE: 50  # compact at 50% context, not 100%
MCPs enabled: < 10 per project     # each MCP adds baseline token cost
```

## When to Use Each

**Put in CLAUDE.md / `rules/common/`:**
- Coding style rules that apply to every task
- Project conventions (naming, patterns)
- Permanent behavioral constraints

**Put in `rules/<language>/` or `rules/<domain>/`:**
- Language-specific standards
- Domain rules (security, payments, migrations)

**Inject via CLI flags:**
- Task-specific context for one session
- Large reference docs only relevant to one feature
- Role/persona overrides

**Use skills:**
- Reusable workflows and domain knowledge invoked on demand

## See Also

- [CLAUDE.md Behavioral Guidelines](claude-md-behavioral-guidelines.md)
- [Claude Code: Context Management Strategy](claude-code-context-management.md)
- [Claude Code: Skill Context Forking & Per-Step Agent Isolation](skill-context-forking.md)
