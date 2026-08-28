---
title: Verification Says What Was Not Checked
impact: HIGH
impactDescription: an all-ticked list hides where the risk actually is
tags: mr, review, testing
---

## Verification Says What Was Not Checked

An unchecked box with a reason is worth more than everything ticked: it tells a reviewer where to
look. State the environment the check ran in, not just that it passed.

**Incorrect:**

```markdown
- [x] Tested
- [x] Works
```

**Correct:**

```markdown
- [x] Staging pipeline green through lint, build, migrate, deploy and verify
- [x] Valid batch returns `204`, rows land in `funnel_events_2026_08`
- [ ] Not run: the production migration, which applies on the tag after this merges
- [ ] Not verified: the `CronJob` firing. It exists only in the production overlay.
```

Reference: `references/mr-templates.md`.
