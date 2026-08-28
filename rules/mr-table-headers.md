---
title: Tables Need Real Headers
impact: MEDIUM
impactDescription: an empty header row renders as a broken table
tags: mr, format, rendering
---

## Tables Need Real Headers

`| | |` produces a visibly empty header row on GitLab. Name the columns.

**Incorrect:**

```markdown
| | |
|---|---|
| Version | 1.2.0 |
```

**Correct:**

```markdown
| Field | Value |
|---|---|
| Version | 1.2.0 |
```

Reference: `references/mr-templates.md`.
