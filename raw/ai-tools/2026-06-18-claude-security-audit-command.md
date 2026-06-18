# Claude Security Audit (/security-audit slash command)

> Source: https://github.com/afiqiqmal/claude-security-audit
> Collected: 2026-06-18
> Published: Unknown

A Claude Code slash command for comprehensive white-box and gray-box security audits. Findings mapped to OWASP Top 10:2025, CWE, NIST CSF 2.0, SANS/CWE Top 25, OWASP ASVS 5.0, PCI DSS 4.0.1, MITRE ATT&CK, SOC 2, ISO 27001:2022. Report saves to `./security-audit-report.md` (+ PDF if a converter is installed).

## Install

```bash
# Claude Code (global, default)
curl -fsSL https://raw.githubusercontent.com/afiqiqmal/claude-security-audit/main/install.sh | bash
# Per-project (command only; references need global install for full/diff modes)
cp -r .claude/commands/security-audit.md /path/to/project/.claude/commands/   # → /project:security-audit
```

Installs `/security-audit` command + reference files to `~/.claude/`. Cross-target via `--target cursor|copilot|windsurf|codex` (installs an equivalent rule/prompt/instructions file + references for that tool). Uninstall: `bash install.sh --uninstall [--target ...]`.

## Features

- White-box: 20 attack categories, 475+ individual checks.
- Gray-box: role-based access probing, API endpoint testing, credential boundary checks, error differential analysis.
- AI/LLM security: prompt injection, output sanitization, RAG poisoning, cost monitoring, tool-calling permissions.
- CWE ID on every finding; NIST CSF 2.0 mapping (Govern/Identify/Protect/Detect/Respond/Recover).
- Security hotspots (sensitive code needing PR review) + code smells (quality patterns that breed bugs).
- Multi-framework auto-detect (up to 3): Laravel, Next.js, FastAPI, Express, Django, Rails, Spring Boot, ASP.NET Core, Go, Flask, Nuxt.js, SvelteKit.
- SARIF/JSON output; baseline suppression; report diff; interactive triage; CI gating; compliance packs; scope exclusions; custom checks; PDF export.

## Modes

| Mode | Phases | Description |
|------|--------|-------------|
| `full` (default) | 1-5 | recon + white-box + gray-box + hotspots + smells |
| `quick` | 1-2 | CRITICAL and HIGH only, no gray-box |
| `gray` | 1, 3 | Gray-box testing only |
| `focus:auth` / `focus:api` / `focus:config` | 1,2,4 | Deep dives |
| `diff` / `diff:BRANCH` | 0,1,2,4 | Scan only git-changed files (fast PR reviews) |
| `recheck:PATH` | 1,2,4 | Re-audit specific files/dirs after fixes |
| `triage` | none | Interactive triage of existing report |
| `phase:1`-`phase:5` | 1+N | Run a single phase |

Phases: 1 recon, 2 white-box analysis, 3 gray-box testing, 4 hotspots, 5 code smells (0 = git diff).

## Flags

| Flag | Effect |
|------|--------|
| `--fix` | Include copy-paste-ready remediation code blocks (off by default; findings-first) |
| `--lite` | OWASP + CWE + NIST only; skip SANS Top 25, ASVS, PCI DSS, ATT&CK, SOC 2, ISO 27001 (reduce tokens) |
| `--fail-on critical\|high\|medium` | CI gating; outputs `SECURITY_AUDIT_EXIT: PASS/FAIL` |
| `--format sarif\|json` | Structured output (SARIF v2.1.0 for GitHub Advanced Security, or JSON) |
| `--update-baseline` | Write finding fingerprints to `.security-audit-baseline.json`; future runs tag `[Known]` |
| `--diff-report PATH` | Compare with previous report (new/resolved/changed-severity) |
| `--pack NAME` | Compliance pack: hipaa, gdpr, fintech, saas-multi-tenant, soc2, education |

## Compliance Packs

hipaa (36 checks, PHI), gdpr (39, consent/data-subject rights), fintech (42, PCI/KYC/AML), saas-multi-tenant (43, tenant isolation), soc2 (50, Trust Service Criteria CC6-CC8), education (35, FERPA/COPPA). Loaded in Phase 1, run as extra checklists in Phase 2.

## Severity Ratings

🔴 CRITICAL (RCE, auth bypass, full breach — fix tonight); 🟠 HIGH (priv esc, account takeover — this sprint); 🟡 MEDIUM (XSS, CSRF, scoped IDOR — next sprint); 🟢 LOW (info disclosure, missing headers); 🔵 INFO (best practice).

## Token Usage (token-intensive; reads command + references + entire codebase)

| Mode | Estimated Total |
|------|-----------------|
| `quick --lite` | ~35-85K |
| `diff --lite` | ~20-45K |
| `full --lite` | ~75-170K |
| `full` | ~90-190K |
| `full --fix` | ~100-210K |

Minimize cost: `quick --lite` for fast checks, `diff --lite` for PR reviews, reserve `full` for thorough audits.

## Custom Checks

Drop `.md` checklists in `~/.claude/security-audit-custom/` (global) or `.claude/security-audit-custom/` (project). Organize under headings with OWASP/NIST tags, e.g. `## Internal API Standards [A01:2025, A05:2025 | PR.AA, PR.DS]`. Loaded Phase 1, run Phase 2. Scope exclusions via `.security-audit-ignore` (gitignore-style).

## CI/CD (GitHub Actions)

```yaml
- run: claude /security-audit "diff:main --lite --fail-on high"
- uses: github/codeql-action/upload-sarif@v3   # with --format sarif
  with: { sarif_file: security-audit-report.sarif }
```

## Report Structure

Executive summary → OWASP coverage matrix → NIST CSF matrix → compliance coverage (omitted with `--lite`) → Critical/High → Medium → Low/Info → Gray-box → Hotspots → Code smells → recommendations (grouped by OWASP) → methodology.

## Conventions

Every finding: OWASP + CWE ID + NIST CSF mapping, exact file path/line, vulnerable code. One finding per issue. Tag format examples: A05:2025, CWE-89, PR.DS, ASVS V5.3.4, PCI DSS 6.2.4, T1190, CC6.1, A.8.28. House style: no em-dashes (use ` - `), no Oxford comma, no AI jargon. License: MIT.
