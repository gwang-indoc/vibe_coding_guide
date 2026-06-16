---
source: user-observation
collected: 2026-06-10
published: Unknown
---

# CLAUDE.md: <important> Tags for Domain-Specific Rules

Wrap domain-specific CLAUDE.md rules in `<important if="...">` tags to stop Claude from ignoring them as files grow longer.

As CLAUDE.md files grow, Claude deprioritizes content. Conditional importance tags signal which rules are critical in specific contexts, preventing silent rule-skipping.
