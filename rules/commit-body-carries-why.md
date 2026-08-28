---
title: The Why Belongs in the Commit Body, Never in a Comment
impact: HIGH
impactDescription: rationale lost at the next refactor; comments rot, commits do not
tags: commits, comments
---

## The Why Belongs in the Commit Body, Never in a Comment

Rationale, tradeoffs, gotchas and bug context go in the commit body. Code says what it does; the
commit says why it is that way. A comment explaining a decision is deleted by the next person who
touches the line, and the reasoning goes with it.

**Incorrect (reasoning stranded in the code):**

```python
# We use list_objects_v2 here because head_object returns 403 even for keys
# that exist, since the bucket credential lacks HeadObject.
listed = client.list_objects_v2(Bucket=bucket, Prefix=key, MaxKeys=1)
```

**Correct (the code is bare, the commit explains):**

```
🐛 fix(api): claim an uploaded original by listing, not HEAD

- the bucket credential is granted PutObject and ListBucket but not HeadObject, so
  head_object answered 403 for keys that plainly exist
- ClientError was swallowed as None, which made a permission failure indistinguishable
  from a missing object
```

Reference: `SKILL.md`, Commits.
