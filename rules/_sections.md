# Sections

Section ordering, impact levels, and what each covers. The section ID in parentheses is the
filename prefix used to group rules.

---

## 1. Commits (commit)

**Impact: HIGH**

The commit log is the only durable record of why a change was made. A subject that does not say
what changed, or a body that omits the reasoning, loses that permanently. Comments in code are
not a substitute and are explicitly not the place for it.

## 2. Branches and flow (flow)

**Impact: CRITICAL**

Protected branches move only by merge request, and production ships only from a tag. Every rule
here prevents an irreversible action: a direct push, a tag on an unsynced branch, a version bump
that does not match what the release contains.

## 3. Merge requests (mr)

**Impact: HIGH**

An MR is the artefact a reviewer reads. Structure, honest verification, and correct labels are
what make a review possible; their absence is what makes a release untraceable six months later.

## 4. Issues (issue)

**Impact: MEDIUM**

Issues here are a board rather than a bug tracker. The rules keep the format light enough to
actually be used, and keep labels honest rather than guessed.

## 5. CI/CD and deploys (ci)

**Impact: CRITICAL**

A green pipeline is not a deployed version. Every rule here exists because something looked
successful and was not.
