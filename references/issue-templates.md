# Issues

Issues here are a board, not a bug tracker. Most are a todo. The format is deliberately light,
because a heavy one gets skipped and an empty issue is worse than a terse one.

The same four formatting rules apply: real table headers, no em dashes, technical terms in
backticks, sentence case.

## Title

Plain formal English, sentence case, stating the outcome. **No emoji, no type prefix, no scope in
parentheses**: that grammar belongs to commits.

```
One link that sends each device to its own store
Scans fail past 55 seconds on mobile uploads
Migrate the mobile repository to the new git host
Verify the socket peer is a Cloudflare edge before trusting its headers
```

Nothing is lost by dropping the prefix. The type is the `type::` label, the area is `area::`, and
the change type is in the commits. A title that repeats them is duplicated data that can
disagree with itself.

State the outcome, not the activity. "Scans fail past 55 seconds" says what is wrong and
therefore what fixed means. "Investigate scan failures" says only that someone will look, and is
still true after the work is done.

**Incorrect:**

```
✨ feat(landing): one link that sends each device to its own store
scan bug
Investigate why uploads are slow
[API] events
```

**Correct:**

```
One link that sends each device to its own store
Scans fail past 55 seconds on mobile uploads
```

## Body

Short. Two sections are required, the rest appear only when they have something in them.

````markdown
## What

Two or three lines on what is wrong or missing, and who it affects. Numbers where they exist:
"8% of scans fail, all iOS" carries more than "scans sometimes fail".

## Acceptance

- [ ] A capture no longer uploads more than the sensor produced
- [ ] A scan on a weak connection completes rather than timing out
- [ ] Verified against production logs, not only locally

## Context

- Production logs, 48h: 6 of 76 scans returned `400` at exactly 60s
- Related: !15, and the `expo-camera` behaviour noted in `scan.tsx:229`
````

Add for `type::bug` only:

````markdown
## Reproduction

1. Open the scan tab on iOS
2. Capture on a connection under about 2 Mbps up
3. The request dies around 55s and the app renders the proxy error page

## Environment

| Field | Value |
|---|---|
| Where | Production |
| Version | `1.1.2`, iOS build 25 |
| Regression | Yes, since `1.0.49`. No, if it has always behaved this way. |
````

`Regression` earns its place because releases are tags: naming the release that introduced a bug
turns a search through history into a diff between two tags.

## Labels

Applied when the issue is opened:

| Label | When | Why |
|---|---|---|
| `type::` | Always | It is the category, which is why there is no per-category template. |
| `area::` | Always | Locates the work. |
| `status::` | Always | `triage` if it needs a decision, `todo` if it is agreed. |
| `priority::` | Always | Not knowing is itself a decision to make, not a reason to skip. |
| `severity::` | Required for `type::bug` | How badly it behaves when it happens, separate from how soon to act. |
| `effort::` | When known | Frequently unknowable before someone looks. |

`priority::` is required because it is a judgement, not data. Every issue competes with every
other for the same attention, and an unprioritised one silently loses that competition: it sits
below anything that was ranked. If it truly does not matter, `priority::low` says so honestly.

`severity::` and `priority::` are not the same axis and should not be collapsed. A cosmetic
misalignment on the pricing page can be `severity::4-trivial` and `priority::high`. A crash
behind a feature flag nobody has enabled can be `severity::1-blocker` and `priority::low`.

`effort::` stays optional. Guessing it is worse than leaving it blank, because it gets trusted
at planning and nobody remembers it was invented at 2am by someone who had not read the code.

### Status lifecycle

Merged to `staging` is not shipped. The card should say so:

| `status::` | Means |
|---|---|
| `triage` | Opened, needs a decision before anyone starts. |
| `todo` | Agreed, not started. |
| `in-progress` | A branch exists. |
| `in-review` | The change MR is open, or merged to `staging` and waiting on a release. |
| `blocked` | Waiting on something external. |
| `done` | Shipped to production. In practice the promotion's `Closes` closes the issue here. |

Nothing moves these automatically. A quick action inside an MR applies to that MR, not to a
linked issue, so the transition is one command alongside opening the MR:

```sh
glab issue update 42 --label "status::in-review" --unlabel "status::in-progress"
```

The `done` end is automatic: the promotion MR's `Closes #42` closes the issue when it merges to
`main`, and a closed card needs no label to say so.

## Linking

An MR that finishes an issue closes it with `Closes #N` in the MR description. `Related #N` links
without closing.

Not every MR has an issue, and that is fine: small fixes go straight to a branch. Write
`Closes | n/a` in the MR summary table rather than dropping the row, so the absence reads as a
decision rather than an oversight.
