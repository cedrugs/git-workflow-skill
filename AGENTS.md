# Git Workflow

**Version 1.0.0**
nutrisi.ai
August 2026

> **Note:**
> This document is for agents and LLMs driving `git`, `glab` and `gh`. Humans may find it
> useful, but the guidance is written for automation, where the failure mode is rarely bad code.
> It is an unlabelled MR, a guessed version bump, a tag cut from an unsynced branch, an issue
> left open because nothing closed it, or a green pipeline reported as a deploy.

Applies to every repository. Where a project's own `CLAUDE.md` contradicts this, the project wins.

## Contents
- [Commits](#commits)
- [Branches and flow](#branches-and-flow)
- [Merge requests](#merge-requests)
- [Issues](#issues)
- [CI/CD and deploys](#cicd-and-deploys)
- [Rules index](#rules-index)

## Commits

### The Why Belongs in the Commit Body, Never in a Comment

**Impact: HIGH** (rationale lost at the next refactor; comments rot, commits do not)


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

### One Commit per Major Change, Never One Dump

**Impact: MEDIUM** (an unreviewable diff, and nothing can be reverted independently)


## One Commit per Major Change, Never One Dump

Group by the change, not by the session. Two unrelated fixes are two commits even when they were
written in the same hour, because a revert should be able to take one without the other.

**Incorrect:**

```
✨ feat(api): funnel events, web billing store, security headers, docs gating
```

**Correct:**

```
✨ feat(api): record web purchases and ingest funnel events
🔒 security(api): send security headers, stop serving the schema
```

Reference: `SKILL.md`, Commits.

### Never Add AI Attribution to a Commit

**Impact: HIGH** (unwanted trailers in permanent history, impossible to remove after a push)


## Never Add AI Attribution to a Commit

No `Co-Authored-By: Claude`, no generated-with trailers, no attribution in files. This applies to
commits, MR descriptions, issues and code comments alike. History is permanent once pushed.

**Incorrect:**

```
🐛 fix(api): bound the size of funnel event params

Co-Authored-By: Claude <noreply@anthropic.com>
```

**Correct:**

```
🐛 fix(api): bound the size of funnel event params
```

Reference: `SKILL.md`, Commits.

### Commit Subjects Carry an Emoji, a Type and a Scope

**Impact: HIGH** (an unscannable log; the type is what drives the release bump)


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

## Branches and flow

### Branch Names Mirror the Commit Type

**Impact: MEDIUM** (the platform's <iid>- names carry no meaning and break the title-to-branch chain)


## Branch Names Mirror the Commit Type

`feat/…`, `fix/…`, `chore/…`, `refactor/…`, `docs/…`, `security/…`, `build/…`, `ci/…`. Never the
platform's generated `<iid>-some-title` form. The branch prefix, the commit type, the MR
`type::` label and the release bump are the same decision expressed four times, so they must
agree.

**Incorrect:**

```
42-fix-the-scan-thing
cedric-patch-1
```

**Correct:**

```
fix/original-claim-listing
security/headers-and-client-ip
```

Reference: `SKILL.md`, Branches.

### The Version Bump Follows the Highest Type in the Release

**Impact: CRITICAL** (a wrong version silently misrepresents what shipped)


## The Version Bump Follows the Highest Type in the Release

`type::feature` promotes as a minor. `type::bug`, `chore` and `docs` promote as a patch. A
breaking change is a major. A promotion containing any feat is a minor even when most of it is
fixes.

**Incorrect (a promotion carrying a feat, bumped as a patch):**

```
1.1.2 -> 1.1.3   # contains ✨ feat(api): ingest funnel events
```

**Correct:**

```
1.1.2 -> 1.2.0
```

Check the range before choosing:

```sh
git log --oneline --no-merges origin/main..origin/staging
```

Reference: `SKILL.md`, The flow.

### staging and main Move Only by Merge Request

**Impact: CRITICAL** (unreviewed code deployed; a fast-forward is not an exemption)


## staging and main Move Only by Merge Request

Never push directly to `staging` or `main`, not even a clean fast-forward. Pushing to `staging`
deploys unreviewed code; pushing to `main` puts something in the release path that no MR
recorded.

**Incorrect:**

```sh
git checkout staging && git merge feat/thing && git push origin staging
```

**Correct:**

```sh
git push -u origin feat/thing
glab mr create --source-branch feat/thing --target-branch staging …
```

Reference: `SKILL.md`, The flow.

### Roll Back by Re-running the Previous Tag

**Impact: HIGH** (a revert commit creates a new release path and a divergent history)


## Roll Back by Re-running the Previous Tag

The previous tag is a known-good, already-built artefact. Re-run its pipeline. Reverting commits
to undo a release adds history that has to be un-reverted later and changes what `main` means.

A migration is the exception worth naming: re-running an older tag does not undo a schema change,
so an MR whose migration cannot be reversed says so in its `Rollback` row.

Reference: `SKILL.md`, Tags.

### Re-sync main Against the Server Before Tagging

**Impact: CRITICAL** (tagging a stale local main ships the wrong commit)


## Re-sync main Against the Server Before Tagging

`git status` reports on your last fetch, not on the server. Merging an MR in the web UI never
reaches your machine, so a local `main` can be "up to date" and still be behind. Use `--ff-only`
so a diverged branch fails loudly rather than tagging a surprise merge commit.

**Incorrect:**

```sh
git checkout main && git tag 1.2.0
```

**Correct:**

```sh
git checkout main && git pull --ff-only origin main
git rev-parse main
git ls-remote origin refs/heads/main    # must match
git ls-remote --tags origin             # confirm the version is free
git tag 1.2.0 && git push origin 1.2.0
```

Reference: `SKILL.md`, Tags.

### Tags Are Bare Semver, Lightweight, No v Prefix

**Impact: CRITICAL** (a v-prefixed tag matches no CI rule and silently deploys nothing)


## Tags Are Bare Semver, Lightweight, No v Prefix

The pipeline matches `^[0-9]+\.[0-9]+\.[0-9]+$`. A `v` prefix matches nothing, so the tag is
created, pushed, and no deploy happens. No `-m`, no annotation.

**Incorrect:**

```sh
git tag -a v1.2.0 -m "release"
```

**Correct:**

```sh
git tag 1.2.0 && git push origin 1.2.0
```

Reference: `SKILL.md`, Tags.

## Merge requests

### Technical Terms Go in Backticks

**Impact: MEDIUM** (bare identifiers in prose are hard to scan and ambiguous)


## Technical Terms Go in Backticks

Identifiers, table and column names, enum values, environment variables, header names, endpoints,
status codes and file paths are inline code. Anything multi-line goes in a fenced block.

**Incorrect:**

```
moves /docs behind DOCS_ENABLED, and prefers cf-connecting-ip over x-forwarded-for
```

**Correct:**

```
Moves `/docs` behind `DOCS_ENABLED`, and prefers `CF-Connecting-IP` over `X-Forwarded-For`
```

Reference: `SKILL.md`, Merge requests.

### Pass the Description From a File

**Impact: MEDIUM** (a heredoc mangles table pipes and mermaid fences)


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

### Link Files at a SHA, Not a Branch

**Impact: MEDIUM** (a branch link stops showing the reviewed content once the branch moves)


## Link Files at a SHA, Not a Branch

A link to `/-/blob/staging/app/main.py` shows whatever `staging` holds today, which is not what
was reviewed. Pin to the commit.

**Correct:**

```sh
BASE=$(git remote get-url origin | sed -E 's|^ssh://git@|https://|; s|\.git$||')
SHA=$(git rev-parse origin/staging)
git diff --numstat origin/main...origin/staging
# BASE/-/blob/SHA/path/to/file.py
```

Group the resulting table inside one `<details>` block per category.

Reference: `references/mr-templates.md`, Building it.

### Link Issues and Prior MRs, or Say n/a

**Impact: MEDIUM** (issues stay open forever; a release cannot be traced to its reviews)


## Link Issues and Prior MRs, or Say n/a

GitLab closes an issue only when the closing pattern reaches the **default branch**. `main` is
the default, so `Closes #N` on a change MR targeting `staging` renders as a link and never fires.
That is worse than omitting it: the issue looks handled and stays open.

| MR | Keyword | Effect |
|---|---|---|
| Change MR, to `staging` | `Related #42` | Links. Nothing closes. |
| Promotion MR, to `main` | `Closes #42` | Closes on merge. |

`Promotes` lists the MRs a release carries. If there is genuinely no issue, write `n/a`: a
missing row reads as an oversight, an explicit `n/a` reads as a decision.

**Incorrect:** omitting the row entirely.

**Correct:**

```markdown
| Promotes | !14, !15, !17 |
| Closes | n/a (no issues open for this work) |
```

Reference: `references/mr-templates.md`, Issue linking.

### No Em Dashes

**Impact: LOW** (house style; an em dash reads as machine-written here)


## No Em Dashes

Use a comma, a colon, or parentheses. Applies to MR bodies, issue bodies and commit messages.

**Incorrect:**

```
Additive only, no field removed, so nothing in the store breaks
```
with an em dash separating the clauses.

**Correct:**

```
Additive only: no field removed, so nothing in the store breaks.
```

Reference: `SKILL.md`, Merge requests.

### Name What Could Break, and What to Do If It Does

**Impact: HIGH** ("low risk" is an opinion; a reviewer needs a fact)


## Name What Could Break, and What to Do If It Does

The `Risk` row names the thing that could break and why it does not. The `Rollback` row says what
to do if it is wrong after merge, and a migration that cannot be undone says so out loud.

**Incorrect:**

```markdown
| Risk | Low |
| Rollback | Easy |
```

**Correct:**

```markdown
| Risk | Additive only, no field removed or renamed. Old clients ignore the new fields. |
| Rollback | Revert the commit and re-run the previous tag. The migration adds nullable columns and does not need unwinding. |
```

Reference: `references/mr-templates.md`.

### MR and Issue Bodies Are Sentence Case

**Impact: LOW** (inconsistent casing reads as unfinished)


## MR and Issue Bodies Are Sentence Case

Every bullet and table value opens with a capital and reads as a sentence. Where a line would
otherwise begin with an identifier, rephrase. Identifiers keep their own casing wherever they
appear.

This differs from commits on purpose: **commit subjects stay lowercase**. The MR body is the
formal artefact a reviewer reads; the log is not.

**Incorrect:**

```
- caps `params` at 2048 bytes serialized
- `occurred_at` is clamped to three days
```

**Correct:**

```
- Caps `params` at 2048 bytes serialized
- Clamps `occurred_at` to three days either side of receipt
```

Reference: `SKILL.md`, Merge requests.

### A Change MR Carries One Label From Each of Six Dimensions

**Impact: HIGH** (an unlabelled MR is invisible to every filter and board)


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

### An MR Description Is Structured, Not Prose

**Impact: HIGH** (a reviewer cannot tell what changed, what is risky, or what was tested)


## An MR Description Is Structured, Not Prose

Summary table, changes, a walkthrough when the change earns one, a collapsible file list, and
verification. Two shapes only: a change MR to `staging`, scoped to one change and labelled, and a
promotion MR to `main`, broad and unlabelled.

Full blocks in `references/mr-templates.md`.

**Incorrect:**

```
adds the events endpoint and some security stuff, tested locally
```

**Correct:** the Change MR template, every row filled, `n/a` where a row does not apply.

Reference: `references/mr-templates.md`.

### Tables Need Real Headers

**Impact: MEDIUM** (an empty header row renders as a broken table)


## Tables Need Real Headers

`| | |` produces a visibly empty header row on GitLab. Name the columns.

**Incorrect:**

```markdown
| | |
|---|---|
| Version | 1.2.0 |
```

**Correct:**

```markdown
| Field | Value |
|---|---|
| Version | 1.2.0 |
```

Reference: `references/mr-templates.md`.

### Verification Says What Was Not Checked

**Impact: HIGH** (an all-ticked list hides where the risk actually is)


## Verification Says What Was Not Checked

An unchecked box with a reason is worth more than everything ticked: it tells a reviewer where to
look. State the environment the check ran in, not just that it passed.

**Incorrect:**

```markdown
- [x] Tested
- [x] Works
```

**Correct:**

```markdown
- [x] Staging pipeline green through lint, build, migrate, deploy and verify
- [x] Valid batch returns `204`, rows land in `funnel_events_2026_08`
- [ ] Not run: the production migration, which applies on the tag after this merges
- [ ] Not verified: the `CronJob` firing. It exists only in the production overlay.
```

Reference: `references/mr-templates.md`.

## Issues

### Keep the Issue Body Light

**Impact: MEDIUM** (a heavy template gets skipped, and a skipped template produces empty issues)


## Keep the Issue Body Light

Issues here are a board, not a bug tracker. `What` and `Acceptance` are required. `Context` when
there is any. `Reproduction` and `Environment` only for `type::bug`, where `Environment` carries a
`Regression` row naming the release that introduced it, which is cheap when releases are tags.

**Correct:**

```markdown
## What

6 of 76 scans returned `400` at exactly 60s over 48h, all iOS. The capture is a 61 MP upscale of
a 4K frame, roughly 20 MB, which outruns the edge on a mobile uplink.

## Acceptance

- [ ] A capture no longer uploads more than the sensor produced
- [ ] Verified against production logs, not only locally
```

Reference: `references/issue-templates.md`, Body.

### An Issue Carries type, area, status and priority on Open

**Impact: MEDIUM** (an unprioritised card silently loses every ranking it is not in)


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

### Do Not Guess effort

**Impact: MEDIUM** (an invented estimate gets trusted at planning)


## Do Not Guess effort

`effort::` is frequently unknowable before someone has read the code. Leaving it blank is honest.
Inventing it is not, because by the time anyone plans against it the guess has become a number
someone believes.

This does not extend to `priority::`, which is required. Priority is a judgement you are able to
make on any issue, and declining to make it is not the same as lacking information.

**Incorrect:** picking `effort::s` so the label set looks complete.

**Correct:** leaving `effort::` unset until someone has looked, and setting it at triage.

Reference: `references/issue-templates.md`, Labels.

### State the Outcome, Not the Activity

**Impact: MEDIUM** (an activity title has no done-condition, so the card never closes cleanly)


## State the Outcome, Not the Activity

"Scans fail past 55s" says what is wrong and therefore what "fixed" means. "Investigate scan
failures" says only that someone will look, and is still true after the work is done.

**Incorrect:**

```
Look into the upload timeouts
```

**Correct:**

```
Scans fail past 55 seconds on mobile uploads
```

Reference: `references/issue-templates.md`, Title.

### status Reflects Where the Work Actually Is

**Impact: MEDIUM** (a card reading done while the change sits unreleased on staging)


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

### Issue and MR Titles Are Plain Formal English

**Impact: MEDIUM** (a prefix duplicating the labels, and a title that reads as machine output)


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

## CI/CD and deploys

### Migrations Are Additive

**Impact: CRITICAL** (a destructive migration breaks the running release mid-deploy)


## Migrations Are Additive

New columns are nullable or carry a default so existing rows stay valid. No destructive changes.
Generate a migration file, never push the schema directly, and check the revision does not
collide with what is already on the integration branch.

Two traps specific to enums:

- A new enum value cannot be used by any statement in the transaction that added it, so the
  `ALTER TYPE` gets its own revision.
- These columns store the member **name**, not the value: `'WEB'`, not `'web'`. A lowercase value
  migrates cleanly and then fails on every insert.

Reference: `SKILL.md`, CI/CD.

### Secrets Come From the Platform and Are Inert Until Pods Roll

**Impact: HIGH** (editing a secret appears to work and changes nothing)


## Secrets Come From the Platform and Are Inert Until Pods Roll

Never commit secrets. Application environment is read once at pod start, so editing a secret
changes nothing until the pods cycle. A deploy that "did not pick up" a new value has usually
just not restarted.

Reference: `SKILL.md`, CI/CD.

### A Green Pipeline Is Not a Deployed Version

**Impact: CRITICAL** (reporting a rollout that never happened)


## A Green Pipeline Is Not a Deployed Version

After a deploy, check the running image tag, not the pipeline's tick. `rollout status` returns
immediately when the image string is unchanged, so re-running a pipeline for the same tag reports
success while the pods keep serving the old build.

**Correct:**

```sh
kubectl -n <ns> get deploy <name> -o jsonpath='{.spec.template.spec.containers[0].image}'
```

Check every workload the tag updates, not only the main deployment: cronjobs carry the image too.

Reference: `SKILL.md`, CI/CD.

## Rules index

| Rule | Section | Impact |
|---|---|---|
| [Migrations Are Additive](rules/ci-migrations-additive.md) | CI/CD | CRITICAL |
| [Secrets Come From the Platform and Are Inert Until Pods Roll](rules/ci-secrets-from-platform.md) | CI/CD | HIGH |
| [A Green Pipeline Is Not a Deployed Version](rules/ci-verify-rolled-image.md) | CI/CD | CRITICAL |
| [The Why Belongs in the Commit Body, Never in a Comment](rules/commit-body-carries-why.md) | Commits | HIGH |
| [One Commit per Major Change, Never One Dump](rules/commit-group-by-change.md) | Commits | MEDIUM |
| [Never Add AI Attribution to a Commit](rules/commit-no-ai-attribution.md) | Commits | HIGH |
| [Commit Subjects Carry an Emoji, a Type and a Scope](rules/commit-subject-format.md) | Commits | HIGH |
| [Branch Names Mirror the Commit Type](rules/flow-branch-name-mirrors-type.md) | Branches and flow | MEDIUM |
| [The Version Bump Follows the Highest Type in the Release](rules/flow-bump-matches-type.md) | Branches and flow | CRITICAL |
| [staging and main Move Only by Merge Request](rules/flow-never-push-protected.md) | Branches and flow | CRITICAL |
| [Roll Back by Re-running the Previous Tag](rules/flow-rollback-by-retag.md) | Branches and flow | HIGH |
| [Re-sync main Against the Server Before Tagging](rules/flow-sync-before-tag.md) | Branches and flow | CRITICAL |
| [Tags Are Bare Semver, Lightweight, No v Prefix](rules/flow-tag-bare-semver.md) | Branches and flow | CRITICAL |
| [Keep the Issue Body Light](rules/issue-body-minimal.md) | Issues | MEDIUM |
| [An Issue Carries type, area, status and priority on Open](rules/issue-labels-on-open.md) | Issues | MEDIUM |
| [Do Not Guess effort](rules/issue-no-guessed-labels.md) | Issues | MEDIUM |
| [State the Outcome, Not the Activity](rules/issue-outcome-not-activity.md) | Issues | MEDIUM |
| [status Reflects Where the Work Actually Is](rules/issue-status-tracks-reality.md) | Issues | MEDIUM |
| [Issue and MR Titles Are Plain Formal English](rules/issue-title-grammar.md) | Issues | MEDIUM |
| [Technical Terms Go in Backticks](rules/mr-backtick-identifiers.md) | Merge requests | MEDIUM |
| [Pass the Description From a File](rules/mr-description-via-file.md) | Merge requests | MEDIUM |
| [Link Files at a SHA, Not a Branch](rules/mr-file-links-immutable-sha.md) | Merge requests | MEDIUM |
| [Link Issues and Prior MRs, or Say n/a](rules/mr-link-issues-and-mrs.md) | Merge requests | MEDIUM |
| [No Em Dashes](rules/mr-no-em-dashes.md) | Merge requests | LOW |
| [Name What Could Break, and What to Do If It Does](rules/mr-risk-and-rollback.md) | Merge requests | HIGH |
| [MR and Issue Bodies Are Sentence Case](rules/mr-sentence-case.md) | Merge requests | LOW |
| [A Change MR Carries One Label From Each of Six Dimensions](rules/mr-six-labels.md) | Merge requests | HIGH |
| [An MR Description Is Structured, Not Prose](rules/mr-structure-required.md) | Merge requests | HIGH |
| [Tables Need Real Headers](rules/mr-table-headers.md) | Merge requests | MEDIUM |
| [Verification Says What Was Not Checked](rules/mr-verification-states-gaps.md) | Merge requests | HIGH |
