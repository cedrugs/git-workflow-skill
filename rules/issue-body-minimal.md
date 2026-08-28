---
title: Keep the Issue Body Light
impact: MEDIUM
impactDescription: a heavy template gets skipped, and a skipped template produces empty issues
tags: issues, format
---

## Keep the Issue Body Light

Issues here are a board, not a bug tracker. `What` and `Acceptance` are required. `Context` when
there is any. `Reproduction` and `Environment` only for `type::bug`, where `Environment` carries a
`Regression` row naming the release that introduced it, which is cheap when releases are tags.

**Correct:**

```markdown
## What

6 of 76 scans returned `400` at exactly 60s over 48h, all iOS. The capture is a 61 MP upscale of
a 4K frame, roughly 20 MB, which outruns the edge on a mobile uplink.

## Acceptance

- [ ] A capture no longer uploads more than the sensor produced
- [ ] Verified against production logs, not only locally
```

Reference: `references/issue-templates.md`, Body.
