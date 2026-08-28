---
title: Commit Subjects Carry an Emoji, a Type and a Scope
impact: HIGH
impactDescription: an unscannable log; the type is what drives the release bump
tags: commits, format
---

## Commit Subjects Carry an Emoji, a Type and a Scope

`<emoji> <type>(<scope>): <short description>`, all lowercase except formal words and technical
terms. The type is not decoration: it is what the MR's `type::` label mirrors, and that label is
what decides whether the promotion is a minor or a patch.

**Incorrect:**

```
Fixed the scan bug
update stuff
WIP
```

**Correct:**

```
🐛 fix(api): claim an uploaded original by listing, not HEAD
✨ feat(landing): revenue-carrying checkout and purchase events
🔧 chore(mobile): give production builds an update channel
```

Reference: `SKILL.md`, the emoji and type table.
