---
title: An MR Description Is Structured, Not Prose
impact: HIGH
impactDescription: a reviewer cannot tell what changed, what is risky, or what was tested
tags: mr, format
---

## An MR Description Is Structured, Not Prose

Summary table, changes, a walkthrough when the change earns one, a collapsible file list, and
verification. Two shapes only: a change MR to `staging`, scoped to one change and labelled, and a
promotion MR to `main`, broad and unlabelled.

Full blocks in `references/mr-templates.md`.

**Incorrect:**

```
adds the events endpoint and some security stuff, tested locally
```

**Correct:** the Change MR template, every row filled, `n/a` where a row does not apply.

Reference: `references/mr-templates.md`.
