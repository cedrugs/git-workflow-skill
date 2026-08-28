---
title: Name What Could Break, and What to Do If It Does
impact: HIGH
impactDescription: "low risk" is an opinion; a reviewer needs a fact
tags: mr, review, risk
---

## Name What Could Break, and What to Do If It Does

The `Risk` row names the thing that could break and why it does not. The `Rollback` row says what
to do if it is wrong after merge, and a migration that cannot be undone says so out loud.

**Incorrect:**

```markdown
| Risk | Low |
| Rollback | Easy |
```

**Correct:**

```markdown
| Risk | Additive only, no field removed or renamed. Old clients ignore the new fields. |
| Rollback | Revert the commit and re-run the previous tag. The migration adds nullable columns and does not need unwinding. |
```

Reference: `references/mr-templates.md`.
