---
title: A Change MR Carries One Label From Each of Six Dimensions
impact: HIGH
impactDescription: an unlabelled MR is invisible to every filter and board
tags: mr, labels
---

## A Change MR Carries One Label From Each of Six Dimensions

`area::`, `effort::`, `priority::`, `severity::`, `status::`, `type::`. Six labels, plus an
assignee. `status::` is always `status::done` on a change MR. Fetch the project's labels first;
the taxonomy is consistent across repos but confirm rather than assume.

A promotion MR carries **no labels and no assignee**.

**Correct:**

```sh
glab mr create --source-branch fix/thing --target-branch staging \
  --title "…" --description "$(cat /tmp/mr.md)" \
  --label "area::api,type::bug,priority::high,severity::3-minor,effort::xs,status::done" \
  --assignee <you> --remove-source-branch --yes
```

Reference: `SKILL.md`, Labels.
