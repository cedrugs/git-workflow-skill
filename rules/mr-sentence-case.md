---
title: MR and Issue Bodies Are Sentence Case
impact: LOW
impactDescription: inconsistent casing reads as unfinished
tags: mr, issues, style
---

## MR and Issue Bodies Are Sentence Case

Every bullet and table value opens with a capital and reads as a sentence. Where a line would
otherwise begin with an identifier, rephrase. Identifiers keep their own casing wherever they
appear.

This differs from commits on purpose: **commit subjects stay lowercase**. The MR body is the
formal artefact a reviewer reads; the log is not.

**Incorrect:**

```
- caps `params` at 2048 bytes serialized
- `occurred_at` is clamped to three days
```

**Correct:**

```
- Caps `params` at 2048 bytes serialized
- Clamps `occurred_at` to three days either side of receipt
```

Reference: `SKILL.md`, Merge requests.
