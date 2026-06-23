# Claude Code: settings.json Permissions

> Sources: User observation, 2026-06-23
> Raw: [Claude Code settings.json Permissions Template](../../raw/ai-tools/2026-06-23-claude-code-settings-json-template.md)
> Updated: 2026-06-23

## Overview

`settings.json` in `.claude/` controls which tools Claude Code may call without prompting. An `allow` list pre-approves safe operations; a `deny` list hard-blocks dangerous ones. Deny takes precedence over allow.

## Structure

```json
{
  "permissions": {
    "allow": [ ... ],
    "deny": [ ... ]
  }
}
```

Rules use tool names (`Read`, `Glob`, `Grep`, `Edit`, `Write`) or `Bash(<pattern>)` with glob wildcards.

## Baseline Template

### Allow

| Rule | Purpose |
|------|---------|
| `Read` | All file reads |
| `Glob` | File pattern matching |
| `Grep` | Content search |
| `Bash(npm run *)` | Package scripts |
| `Bash(pnpm *)` | pnpm commands |
| `Bash(git status)` | Git state inspection |
| `Bash(git diff *)` | Git diffs |
| `Bash(git log *)` | Git history |
| `Bash(node *)` | Node runtime |
| `Bash(npx *)` | Package runner |

### Deny

| Rule | Blocks |
|------|--------|
| `Bash(rm -rf *)` | Recursive deletion |
| `Bash(* --force)` | Force flags on any command |
| `Bash(curl *)` | Arbitrary HTTP requests |
| `Bash(wget *)` | Arbitrary downloads |
| `Read(./.env)` | Local env secrets |
| `Read(./.env.*)` | Dotenv variants |
| `Edit(./.env)` | Env file writes |
| `Edit(./.env.*)` | Dotenv variant writes |
| `Read(~/.ssh/*)` | SSH keys |
| `Read(~/.aws/*)` | AWS credentials |

## Key Principles

- **Deny overrides allow** — if a pattern matches both lists, deny wins.
- **Glob wildcards** — `*` matches any substring in `Bash()` patterns; path patterns use standard glob.
- **Scope** — project `.claude/settings.json` applies to that project; `~/.claude/settings.json` applies globally.
- **Prompt on miss** — any tool call not matched by allow/deny prompts the user at runtime.

## See Also

- [Claude Code: Dynamic Context Injection](claude-code-dynamic-context-injection.md)
- [ECC: Claude Code Plugin](ecc-claude-code-plugin.md)
