---
title: The Version Bump Follows the Highest Type in the Release
impact: CRITICAL
impactDescription: a wrong version silently misrepresents what shipped
tags: flow, semver, release
---

## The Version Bump Follows the Highest Type in the Release

`type::feature` promotes as a minor. `type::bug`, `chore` and `docs` promote as a patch. A
breaking change is a major. A promotion containing any feat is a minor even when most of it is
fixes.

**Incorrect (a promotion carrying a feat, bumped as a patch):**

```
1.1.2 -> 1.1.3   # contains ✨ feat(api): ingest funnel events
```

**Correct:**

```
1.1.2 -> 1.2.0
```

Check the range before choosing:

```sh
git log --oneline --no-merges origin/main..origin/staging
```

Reference: `SKILL.md`, The flow.
