---
title: An Issue Carries type, area, status and priority on Open
impact: MEDIUM
impactDescription: an unprioritised card silently loses every ranking it is not in
tags: issues, labels
---

## An Issue Carries type, area, status and priority on Open

| Label | When |
|---|---|
| `type::` | Always. It is the category, which is why there is no per-category template. |
| `area::` | Always. |
| `status::` | Always. `triage` if it needs a decision, `todo` if it is agreed. |
| `priority::` | Always. |
| `severity::` | Required for `type::bug`. |
| `effort::` | When known. |

`priority::` is required because it is a judgement rather than data. Every issue competes with
every other for the same attention, and an unprioritised one loses that competition silently: it
sorts below anything that was ranked, without anyone deciding it should. If it genuinely does not
matter, `priority::low` says so honestly and is still a decision.

`severity::` is a different axis and belongs on bugs, where "how badly does it behave" is a real
question. A cosmetic misalignment on the pricing page is `severity::4-trivial` and
`priority::high`. A crash behind a flag nobody has enabled is `severity::1-blocker` and
`priority::low`. Collapsing the two loses both.

**Correct:**

```sh
glab issue create --title "Scans fail past 55 seconds on mobile uploads" \
  --description "$(cat /tmp/issue.md)" \
  --label "type::bug,area::api,status::todo,priority::high,severity::2-major"
```

Reference: `references/issue-templates.md`, Labels.
