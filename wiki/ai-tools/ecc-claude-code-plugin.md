# ECC: Claude Code Plugin

> Sources: ECC (affaan-m), 2026-06-06
> Raw: [ECC: Harness-Native Operator System](../../raw/ai-tools/2026-06-06-ecc-harness-native-operator-system.md)
> Updated: 2026-06-06

## Overview

Comprehensive Claude Code plugin: 63 subagents, 251 skills, hooks system, structured rules architecture. Cross-platform (Claude Code, Cursor, Codex, OpenCode, GitHub Copilot, Zed).

## Install

```bash
/plugin install ecc@ecc
```

**Warning:** do not stack install methods. Plugin install OR install.sh — not both. Stacking is the most common broken setup.

## Rules Architecture

```
rules/
  common/        ← language-agnostic
  typescript/
  python/
  go/
  swift/
  php/
  harmonyos/
```

Claude Code v2.1+ auto-loads `hooks/hooks.json` from installed plugins.

## Token Optimization Defaults

| Setting | Value | Reason |
|---|---|---|
| `model` | `sonnet` | Cost baseline |
| `MAX_THINKING_TOKENS` | `10,000` | Cap extended thinking |
| `CLAUDE_AUTOCOMPACT_PCT_OVERRIDE` | `50` | Compact at 50%, not 100% |
| MCPs enabled | `< 10` | Each MCP adds baseline cost |

## Skills

251 skills — one domain each, under 800 lines, with "When to Activate" triggers. Invoked on demand, not loaded every session.

## Hooks

Trigger on: `PreToolUse`, `PostToolUse`, `SessionStart`, `Stop`. Use for validation, formatting, secret detection.

## AgentShield

Security scanning: 1,282 tests, 102 rules. Covers secrets detection, permission auditing, MCP risk profiling. Run via `/security-scan`.

## See Also

- [Claude Code: Dynamic Context Injection](claude-code-dynamic-context-injection.md)
- [Claude Code: Context Management Strategy](claude-code-context-management.md)
- [Claude HUD: Statusline Plugin for Claude Code](claude-hud-statusline-plugin.md)
