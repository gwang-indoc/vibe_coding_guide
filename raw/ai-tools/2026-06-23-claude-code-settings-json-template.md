# Claude Code settings.json Permissions Template

> Source: User-provided template
> Collected: 2026-06-23
> Published: Unknown

```json
{
  "permissions": {
    "allow": [
      "Read",
      "Glob",
      "Grep",
      "Bash(npm run *)",
      "Bash(pnpm *)",
      "Bash(git status)",
      "Bash(git diff *)",
      "Bash(git log *)",
      "Bash(node *)",
      "Bash(npx *)"
    ],
    "deny": [
      "Bash(rm -rf *)",
      "Bash(* --force)",
      "Bash(curl *)",
      "Bash(wget *)",
      "Read(./.env)",
      "Read(./.env.*)",
      "Edit(./.env)",
      "Edit(./.env.*)",
      "Read(~/.ssh/*)",
      "Read(~/.aws/*)"
    ]
  }
}
```

**Allow list rationale:**
- `Read`, `Glob`, `Grep` — safe read-only operations, no side effects
- `Bash(npm run *)`, `Bash(pnpm *)` — project script execution via package manager
- `Bash(git status)`, `Bash(git diff *)`, `Bash(git log *)` — read-only git inspection
- `Bash(node *)`, `Bash(npx *)` — node runtime and package runner

**Deny list rationale:**
- `Bash(rm -rf *)` — prevents recursive deletion
- `Bash(* --force)` — blocks force flags on any command (git push --force, etc.)
- `Bash(curl *)`, `Bash(wget *)` — blocks arbitrary network requests
- `Read(./.env)`, `Read(./.env.*)`, `Edit(./.env)`, `Edit(./.env.*)` — protects local secrets
- `Read(~/.ssh/*)`, `Read(~/.aws/*)` — protects SSH keys and AWS credentials
