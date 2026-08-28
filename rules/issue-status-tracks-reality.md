---
title: status Reflects Where the Work Actually Is
impact: MEDIUM
impactDescription: a card reading done while the change sits unreleased on staging
tags: issues, labels, workflow
---

## status Reflects Where the Work Actually Is

Merged to `staging` is not shipped. A change waits there until a promotion and a tag, which can
be days. The card should read `status::in-review` for that whole window, not `status::done`.

| `status::` | Means |
|---|---|
| `triage` | Needs a decision before anyone starts |
| `todo` | Agreed, not started |
| `in-progress` | A branch exists |
| `in-review` | Change MR open, or merged to `staging` and awaiting a release |
| `blocked` | Waiting on something external |
| `done` | Shipped to production |

Nothing moves these automatically. A quick action inside an MR applies to that MR, not to a
linked issue, so the transition is an explicit command when the MR opens:

```sh
glab issue update 42 --label "status::in-review" --unlabel "status::in-progress"
```

The `done` end needs no command: the promotion MR's `Closes #42` closes the issue on merge to
`main`, which is the moment it becomes true.

Note this is about the **issue**. A change MR's own `status::done` is correct on merge, because
the MR is finished even though the release is not.

Reference: `references/issue-templates.md`, Status lifecycle.
