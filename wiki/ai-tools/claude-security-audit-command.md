# Claude Security Audit (/security-audit)

> Sources: afiqiqmal (claude-security-audit), 2026-06-18
> Raw: [Claude Security Audit (/security-audit slash command)](../../raw/ai-tools/2026-06-18-claude-security-audit-command.md)
> Updated: 2026-06-18

## Overview

An **active** Claude Code slash command that runs white-box and gray-box security audits over a codebase and writes `./security-audit-report.md` (+ PDF if a converter exists). 20 attack categories, 475+ checks. Every finding is tagged with OWASP Top 10:2025 + CWE + NIST CSF 2.0, plus SANS Top 25 / ASVS 5.0 / PCI DSS 4.0.1 / MITRE ATT&CK / SOC 2 / ISO 27001 (unless `--lite`). Contrast with the [OWASP Security Skill](owasp-security-skill.md), which is passive in-context knowledge rather than a report-producing scan.

## Install

```bash
curl -fsSL https://raw.githubusercontent.com/afiqiqmal/claude-security-audit/main/install.sh | bash
```

Installs `/security-audit` + references to `~/.claude/`. Per-project copies only the command (`/project:security-audit`); references must be global for full/diff modes. Cross-target: `--target cursor|copilot|windsurf|codex` installs an equivalent rule/prompt for that tool.

## Modes

| Mode | Phases | Use |
|------|--------|-----|
| `full` (default) | 1-5 | recon + white-box + gray-box + hotspots + smells |
| `quick` | 1-2 | CRITICAL/HIGH only, no gray-box |
| `gray` | 1,3 | gray-box only |
| `focus:auth\|api\|config` | 1,2,4 | targeted deep dive |
| `diff` / `diff:BRANCH` | 0,1,2,4 | only git-changed files (PR reviews) |
| `recheck:PATH` | 1,2,4 | re-audit after fixes |
| `triage` | — | interactive Accept/Defer/Dismiss/Escalate |
| `phase:N` | 1+N | single phase |

## Flags

`--fix` (add remediation code; findings-first by default) · `--lite` (OWASP+CWE+NIST only, cheaper) · `--fail-on critical\|high\|medium` (CI gating, emits `SECURITY_AUDIT_EXIT: PASS/FAIL`) · `--format sarif\|json` · `--update-baseline` (fingerprints → `[Known]` tags) · `--diff-report PATH` · `--pack hipaa\|gdpr\|fintech\|saas-multi-tenant\|soc2\|education`.

## Distinguishing Capabilities

- **Gray-box testing** — role-based access probing, API verb tampering / mass assignment, credential boundary checks (expired tokens, tenant isolation), error differential analysis (fail-open / existence leaks). Unique vs the passive skill.
- **Framework auto-detect** (up to 3) — Laravel, Next.js, FastAPI, Express, Django, Rails, Spring Boot, ASP.NET Core, Go, Flask, Nuxt.js, SvelteKit; loads framework-specific checks.
- **Hotspots + code smells** — flags sensitive code for PR review and quality patterns that breed bugs.
- **CI integration** — `diff:main --lite --fail-on high` in GitHub Actions; SARIF upload to GitHub Advanced Security.

## Token Cost (reads command + references + whole codebase)

`diff --lite` ~20-45K · `quick --lite` ~35-85K · `full --lite` ~75-170K · `full` ~90-190K · `full --fix` ~100-210K. Use `diff --lite` for PRs; reserve `full` for thorough audits.

## Conventions

One finding per issue, exact path/line + vulnerable code. House style: no em-dashes, no Oxford comma, no AI jargon. Severity emoji: 🔴 CRITICAL → 🔵 INFO. License MIT.

## See Also

- [OWASP Security Skill for Claude Code](owasp-security-skill.md) — passive knowledge skill on the same OWASP 2025 baseline
- [ECC: Claude Code Plugin](ecc-claude-code-plugin.md) — bundles `/security-scan` (AgentShield) for secrets/permissions/MCP risk
