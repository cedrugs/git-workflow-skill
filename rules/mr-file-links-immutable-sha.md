---
title: Link Files at a SHA, Not a Branch
impact: MEDIUM
impactDescription: a branch link stops showing the reviewed content once the branch moves
tags: mr, format, review
---

## Link Files at a SHA, Not a Branch

A link to `/-/blob/staging/app/main.py` shows whatever `staging` holds today, which is not what
was reviewed. Pin to the commit.

**Correct:**

```sh
BASE=$(git remote get-url origin | sed -E 's|^ssh://git@|https://|; s|\.git$||')
SHA=$(git rev-parse origin/staging)
git diff --numstat origin/main...origin/staging
# BASE/-/blob/SHA/path/to/file.py
```

Group the resulting table inside one `<details>` block per category.

Reference: `references/mr-templates.md`, Building it.
