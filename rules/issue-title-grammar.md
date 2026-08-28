---
title: Issue and MR Titles Are Plain Formal English
impact: MEDIUM
impactDescription: a prefix duplicating the labels, and a title that reads as machine output
tags: issues, mr, format
---

## Issue and MR Titles Are Plain Formal English

The `<emoji> <type>(<scope>):` grammar belongs to **commits only**. An issue or MR title is
sentence case, states the outcome, and carries no emoji, no type prefix and no scope in
parentheses.

Nothing is lost by dropping it. The type lives in the `type::` label, the area in `area::`, and
the change type in the commits the MR contains. Repeating it in the title is duplicated data that
can disagree with itself.

**Incorrect:**

```
✨ feat(landing): one link that sends each device to its own store
🐛 fix(api): scans fail past 55s on mobile uploads
🔒 security(api): security headers, disable public schema
```

**Correct:**

```
One link that sends each device to its own store
Scans fail past 55 seconds on mobile uploads
Security headers, a private schema, and an edge-trusted client IP
```

Commits keep the grammar:

```
✨ feat(landing): one link that sends each device to its own store
```

Reference: `references/issue-templates.md`, Title.
