---
title: Do Not Guess effort
impact: MEDIUM
impactDescription: an invented estimate gets trusted at planning
tags: issues, labels
---

## Do Not Guess effort

`effort::` is frequently unknowable before someone has read the code. Leaving it blank is honest.
Inventing it is not, because by the time anyone plans against it the guess has become a number
someone believes.

This does not extend to `priority::`, which is required. Priority is a judgement you are able to
make on any issue, and declining to make it is not the same as lacking information.

**Incorrect:** picking `effort::s` so the label set looks complete.

**Correct:** leaving `effort::` unset until someone has looked, and setting it at triage.

Reference: `references/issue-templates.md`, Labels.
