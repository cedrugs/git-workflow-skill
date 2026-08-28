---
title: Re-sync main Against the Server Before Tagging
impact: CRITICAL
impactDescription: tagging a stale local main ships the wrong commit
tags: flow, tags, release
---

## Re-sync main Against the Server Before Tagging

`git status` reports on your last fetch, not on the server. Merging an MR in the web UI never
reaches your machine, so a local `main` can be "up to date" and still be behind. Use `--ff-only`
so a diverged branch fails loudly rather than tagging a surprise merge commit.

**Incorrect:**

```sh
git checkout main && git tag 1.2.0
```

**Correct:**

```sh
git checkout main && git pull --ff-only origin main
git rev-parse main
git ls-remote origin refs/heads/main    # must match
git ls-remote --tags origin             # confirm the version is free
git tag 1.2.0 && git push origin 1.2.0
```

Reference: `SKILL.md`, Tags.
