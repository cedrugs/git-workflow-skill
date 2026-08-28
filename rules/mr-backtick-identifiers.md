---
title: Technical Terms Go in Backticks
impact: MEDIUM
impactDescription: bare identifiers in prose are hard to scan and ambiguous
tags: mr, issues, format
---

## Technical Terms Go in Backticks

Identifiers, table and column names, enum values, environment variables, header names, endpoints,
status codes and file paths are inline code. Anything multi-line goes in a fenced block.

**Incorrect:**

```
moves /docs behind DOCS_ENABLED, and prefers cf-connecting-ip over x-forwarded-for
```

**Correct:**

```
Moves `/docs` behind `DOCS_ENABLED`, and prefers `CF-Connecting-IP` over `X-Forwarded-For`
```

Reference: `SKILL.md`, Merge requests.
