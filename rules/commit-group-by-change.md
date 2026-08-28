---
title: One Commit per Major Change, Never One Dump
impact: MEDIUM
impactDescription: an unreviewable diff, and nothing can be reverted independently
tags: commits, review
---

## One Commit per Major Change, Never One Dump

Group by the change, not by the session. Two unrelated fixes are two commits even when they were
written in the same hour, because a revert should be able to take one without the other.

**Incorrect:**

```
✨ feat(api): funnel events, web billing store, security headers, docs gating
```

**Correct:**

```
✨ feat(api): record web purchases and ingest funnel events
🔒 security(api): send security headers, stop serving the schema
```

Reference: `SKILL.md`, Commits.
