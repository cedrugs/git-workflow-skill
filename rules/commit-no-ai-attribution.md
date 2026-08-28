---
title: Never Add AI Attribution to a Commit
impact: HIGH
impactDescription: unwanted trailers in permanent history, impossible to remove after a push
tags: commits, policy
---

## Never Add AI Attribution to a Commit

No `Co-Authored-By: Claude`, no generated-with trailers, no attribution in files. This applies to
commits, MR descriptions, issues and code comments alike. History is permanent once pushed.

**Incorrect:**

```
🐛 fix(api): bound the size of funnel event params

Co-Authored-By: Claude <noreply@anthropic.com>
```

**Correct:**

```
🐛 fix(api): bound the size of funnel event params
```

Reference: `SKILL.md`, Commits.
