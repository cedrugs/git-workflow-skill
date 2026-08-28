---
title: Tags Are Bare Semver, Lightweight, No v Prefix
impact: CRITICAL
impactDescription: a v-prefixed tag matches no CI rule and silently deploys nothing
tags: flow, tags, release
---

## Tags Are Bare Semver, Lightweight, No v Prefix

The pipeline matches `^[0-9]+\.[0-9]+\.[0-9]+$`. A `v` prefix matches nothing, so the tag is
created, pushed, and no deploy happens. No `-m`, no annotation.

**Incorrect:**

```sh
git tag -a v1.2.0 -m "release"
```

**Correct:**

```sh
git tag 1.2.0 && git push origin 1.2.0
```

Reference: `SKILL.md`, Tags.
