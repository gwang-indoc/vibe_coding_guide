# OWASP Security Skill for Claude Code

> Sources: agamm (claude-code-owasp), 2026-06-18
> Raw: [OWASP Security Skill for Claude Code (owasp-security)](../../raw/ai-tools/2026-06-18-owasp-security-skill-claude-code.md)
> Updated: 2026-06-18

## Overview

A Claude Code **skill** (passive, always-loaded knowledge) that injects current OWASP 2025-2026 standards into context whenever you write or review security-sensitive code. `allowed-tools: Read Grep Glob` — it reasons over code, does not run scans or write reports. Contrast with [Claude Security Audit](claude-security-audit-command.md), which is an active `/security-audit` command that produces a report.

## Install

```bash
# Per-project
npx degit agamm/claude-code-owasp/.claude/skills/owasp-security .claude/skills/owasp-security
# Global (all projects)
npx degit agamm/claude-code-owasp/.claude/skills/owasp-security ~/.claude/skills/owasp-security
```

## Structure (progressive disclosure)

| File | Loaded | Content |
|---|---|---|
| `SKILL.md` | Always | Top 10:2025 table, review checklists, secure patterns, LLM Top 10, Agentic ASI01-10, ASVS 5.0 |
| `reference/languages.md` | On demand | Unsafe/safe quirks for 20+ languages |
| `reference/owasp-report.md` | On demand | Deep-dive on every 2025-2026 standard |

Auto-activates when reviewing code for vulns, implementing auth, handling user input, doing crypto/password storage, designing APIs, or building AI agent / LLM systems.

## Standards Covered

- **OWASP Top 10:2025** — based on 175k+ CVEs, 2.8M apps. Notable shifts: A03 Software Supply Chain Failures (NEW), A10 Mishandling of Exceptional Conditions (NEW, fail-open/crashes), SSRF folded into A01, A02 Misconfiguration up to #2.
- **OWASP Top 10 for LLM Applications 2025** — LLM01-LLM10 for chatbots/RAG/tool-calling apps (prompt injection, improper output handling, excessive agency, unbounded consumption, etc.).
- **OWASP Agentic AI 2026** — ASI01-ASI10 for agents/multi-agent systems (goal hijacking, tool misuse, memory/context poisoning, rogue agents, cascading failures).
- **ASVS 5.0** (May 2025) — ~350 reqs, 17 categories, L1/L2/L3; new chapters V15 OAuth/OIDC, V16 Self-Contained Tokens, V17 WebSockets.

## Core Patterns

Each standard ships unsafe→safe code pairs: parameterized queries, `subprocess(shell=False)`, Argon2 for passwords, fail-closed auth (deny on error), prompt-injection boundaries (`<user_data>` markers), treat-LLM-output-as-untrusted, least-privilege scoped agent tokens, per-user rate/token caps.

## Deep Analysis Mindset

For any language not in `languages.md`, reason like a senior researcher across: memory model, type system, serialization (every language has a pickle equivalent), concurrency/TOCTOU, FFI boundaries, std-lib CVEs, package ecosystem attacks, build injection, debug/release differences, fail-open vs fail-closed.

## See Also

- [Claude Security Audit](claude-security-audit-command.md) — active audit command covering the same OWASP 2025 baseline
- [ECC: Claude Code Plugin](ecc-claude-code-plugin.md) — bundles AgentShield `/security-scan` for secrets/permissions/MCP risk
