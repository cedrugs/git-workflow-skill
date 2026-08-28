---
title: Link Issues and Prior MRs, or Say n/a
impact: MEDIUM
impactDescription: issues stay open forever; a release cannot be traced to its reviews
tags: mr, issues, traceability
---

## Link Issues and Prior MRs, or Say n/a

GitLab closes an issue only when the closing pattern reaches the **default branch**. `main` is
the default, so `Closes #N` on a change MR targeting `staging` renders as a link and never fires.
That is worse than omitting it: the issue looks handled and stays open.

| MR | Keyword | Effect |
|---|---|---|
| Change MR, to `staging` | `Related #42` | Links. Nothing closes. |
| Promotion MR, to `main` | `Closes #42` | Closes on merge. |

A closing keyword also has to sit on its own line. GitLab needs `Closes` and `#42` separated by
nothing but whitespace or a colon, and a table cell puts a `|` between them, so a `Closes` row
renders correctly and parses as nothing. Keep the row for the reader and repeat the keyword as a
plain line under the table.

`Promotes` lists the MRs a release carries. If there is genuinely no issue, write `n/a`: a
missing row reads as an oversight, an explicit `n/a` reads as a decision.

**Incorrect:** omitting the row, or leaving a real `Closes` only in the table.

```markdown
| Closes | #42 |
```

**Correct:**

```markdown
| Promotes | !14, !15, !17 |
| Closes | #42 (as the line below, which is what fires) |

Closes #42
```

Confirm it took, rather than assuming, because a silent miss looks identical to success:

```sh
glab api "projects/<ns>%2F<repo>/issues/42/closed_by"   # empty means nothing will close it
```

Reference: `references/mr-templates.md`, Issue linking.
