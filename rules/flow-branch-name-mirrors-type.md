---
title: Branch Names Mirror the Commit Type
impact: MEDIUM
impactDescription: the platform's <iid>- names carry no meaning and break the title-to-branch chain
tags: flow, branches
---

## Branch Names Mirror the Commit Type

`feat/…`, `fix/…`, `chore/…`, `refactor/…`, `docs/…`, `security/…`, `build/…`, `ci/…`. Never the
platform's generated `<iid>-some-title` form. The branch prefix, the commit type, the MR
`type::` label and the release bump are the same decision expressed four times, so they must
agree.

**Incorrect:**

```
42-fix-the-scan-thing
cedric-patch-1
```

**Correct:**

```
fix/original-claim-listing
security/headers-and-client-ip
```

Reference: `SKILL.md`, Branches.
