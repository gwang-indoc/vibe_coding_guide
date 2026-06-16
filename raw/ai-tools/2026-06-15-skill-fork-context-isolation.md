# Skill fork + per-step agent context isolation

> Source: User-provided tip (conversation)
> Collected: 2026-06-15
> Published: Unknown

add 'fork:true' to a skill's frontmatter to have it run in its own context window, then in the skill tell it to use agents to keep context isolated for each step also. Adding this to the built in /code-review skill to improve performance even more.

---

## Verification note (added during ingest, 2026-06-15)

Checked against Claude Code docs via claude-code-guide agent:

- **Key name is wrong.** The real frontmatter key is `context: fork`, not `fork: true`. Documented in the SKILL.md frontmatter table; added in Claude Code v2.1.0 (2026-01-07). `context: fork` runs the skill in a forked subagent context — isolated context window, fresh environment, not sharing parent conversation history, prior skills, or already-read files.
- **Per-step subagent isolation is real and supported.** Docs (sub-agents.md) explicitly describe directing Claude to use subagents in sequence for multi-step workflows; each returns results to the parent, which passes relevant context onward. Nested subagents up to 5 levels deep (v2.1.172+).
- **`/code-review` is a built-in bundled skill** (reviews diffs for bugs + simplification/efficiency cleanups). BUT bundled skills are not user-editable — you cannot add `context: fork` to its frontmatter. The performance-improvement claim for `/code-review` is unverified and not applicable as stated.
