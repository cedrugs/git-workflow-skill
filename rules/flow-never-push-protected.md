---
title: staging and main Move Only by Merge Request
impact: CRITICAL
impactDescription: unreviewed code deployed; a fast-forward is not an exemption
tags: flow, protected-branches
---

## staging and main Move Only by Merge Request

Never push directly to `staging` or `main`, not even a clean fast-forward. Pushing to `staging`
deploys unreviewed code; pushing to `main` puts something in the release path that no MR
recorded.

**Incorrect:**

```sh
git checkout staging && git merge feat/thing && git push origin staging
```

**Correct:**

```sh
git push -u origin feat/thing
glab mr create --source-branch feat/thing --target-branch staging …
```

Reference: `SKILL.md`, The flow.
