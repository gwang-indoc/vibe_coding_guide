---
Source: User observation (direct input)
Collected: 2026-06-19
Published: Unknown
---

# Claude Code Agent: Manual Milestone Compaction

For Claude Code agents, do not wait until the context limit. Use manual milestone compaction:

After repo exploration → /compact
After implementation plan → /compact
After major code changes → /compact
After E2E test result → /compact
Before handoff to another agent → /compact

When context usage reaches 60-70%, it is better to use /compact.
