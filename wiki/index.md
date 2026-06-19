# Knowledge Base Index

## ai-tools

Tools, plugins, and skills for AI coding agents.

| Article | Summary | Updated |
|---------|---------|---------|
| [AI Output Control Strategies](ai-tools/ai-output-control-strategies.md) | Two strategies to cut AI verbosity: caveman output style plugin (persistent) or terse inline directive (one-off); when NOT to use caveman output style | 2026-06-01 |
| [CLAUDE.md Behavioral Guidelines](ai-tools/claude-md-behavioral-guidelines.md) | 4 rules for CLAUDE.md: think before coding, simplicity first, surgical changes, goal-driven execution; <200 lines per file; `<important if="...">` tags for domain rules | 2026-06-10 |
| [Claude Code: Model Selection by Task](ai-tools/claude-code-model-selection.md) | Haiku/Sonnet/Opus decision table by task type; default Sonnet, escalate to Opus only when Sonnet loops or misses root cause | 2026-06-03 |
| [CodeGraph: Codebase Knowledge Graph Tool](ai-tools/codegraph-codebase-knowledge-graph.md) | TypeScript, MCP-native, pure AST (no LLM), SQLite, zero-config; vendor bench: 16% cheaper / 58% fewer tool calls (architecture Qs only); independent test on mid-size repo: adds cost except for full-stack trace — may benefit huge repos | 2026-06-03 |
| [Graphify: Codebase Knowledge Graph Tool](ai-tools/graphify-codebase-knowledge-graph.md) | Python tool (58.8k stars) for codebase knowledge graphs; only worth using on huge repos — real test on mid-size repo showed no token savings vs no-Graphify ($0.31 vs $0.31) | 2026-06-03 |
| [Claude Code: Context Management Strategy](ai-tools/claude-code-context-management.md) | Explore→plan→clear→execute; dumb zone ~40%; rewind (double-Esc//rewind) over in-context correction; compact at phase boundaries; agent milestone compaction at repo-explore/plan/code/E2E/handoff (60-70% threshold) | 2026-06-19 |
| [Claude Code: Dynamic Context Injection](ai-tools/claude-code-dynamic-context-injection.md) | CLI flags + ECC structured rules dirs + skills as on-demand context; token optimization defaults; when to use each mechanism | 2026-06-06 |
| [ECC: Claude Code Plugin](ai-tools/ecc-claude-code-plugin.md) | 63 subagents, 251 skills, structured rules/common + language dirs, hooks system; token optimization table; /security-scan | 2026-06-06 |
| [Claude HUD: Statusline Plugin for Claude Code](ai-tools/claude-hud-statusline-plugin.md) | Real-time terminal statusline via native Claude Code statusline API; shows context usage, rate limits, git, tools, agents, todos; ~300ms updates; expanded/compact layout; most elements off by default | 2026-06-08 |
| [Claude Code: Skill Context Forking & Per-Step Agent Isolation](ai-tools/skill-context-forking.md) | `context: fork` frontmatter (NOT `fork: true`) runs a skill in isolated context (v2.1.0); skills can direct per-step subagent isolation; built-in `/code-review` is bundled and NOT user-editable so can't take the flag | 2026-06-15 |
| [Claude Code: Auto Mode + Parallel Sessions Workflow](ai-tools/auto-mode-parallel-workflow.md) | Run Claude in auto-accept mode unattended, parallelize across sessions instead of babysitting; plan mode optional for the written artifact; Boris runs auto mode for everything | 2026-06-15 |
| [OWASP Security Skill for Claude Code](ai-tools/owasp-security-skill.md) | Passive always-loaded skill (agamm) injecting OWASP 2025-2026 standards: Top 10:2025, LLM Top 10, Agentic ASI01-10, ASVS 5.0; 20+ language unsafe/safe quirks; install via degit | 2026-06-18 |
| [Claude Security Audit (/security-audit)](ai-tools/claude-security-audit-command.md) | Active audit command (afiqiqmal): white-box + gray-box, 475+ checks, findings mapped to OWASP 2025/CWE/NIST + 5 more frameworks; diff/quick/focus modes, --fix/--lite/--fail-on, compliance packs, SARIF/CI | 2026-06-18 |
