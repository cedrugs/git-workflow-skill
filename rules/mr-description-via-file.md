---
title: Pass the Description From a File
impact: MEDIUM
impactDescription: a heredoc mangles table pipes and mermaid fences
tags: mr, tooling
---

## Pass the Description From a File

Write the body to a file, then pass it. Piping a heredoc straight into `glab` corrupts table
pipes and nested code fences often enough that it is not worth diagnosing each time.

A related trap: an example block containing a mermaid fence must use a four-backtick outer fence,
or the inner triple backticks close it early.

**Correct:**

```sh
glab mr create … --description "$(cat /tmp/mr.md)"
glab mr update 16 --description "$(cat /tmp/mr.md)"
```

Reference: `references/mr-templates.md`.
