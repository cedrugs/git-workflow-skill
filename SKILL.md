---
name: git-workflow
description: House conventions for commits, branches, merge requests, labels, promotion bumps, tags and the CI/CD pipeline. Load before writing any commit message, opening or reviewing an MR/PR, choosing a branch name, promoting to main, or cutting a release tag: and whenever a task involves git, glab, gh or a deploy pipeline.
---

# Git workflow

Applies to every repository. Where a project's own CLAUDE.md contradicts this, the project wins.

| File | What it holds |
|---|---|
| `AGENTS.md` | Every rule inlined with examples. Generated from `rules/`. |
| `rules/` | One file per rule, 29 across five sections. |
| `references/mr-templates.md` | Change MR and Promotion MR blocks, and the commands that build their tables. |
| `references/issue-templates.md` | Issue title grammar, body, labels and linking. |
| `README.md` | Structure, and how to add a rule. |

## Branches

Named after the commit type that dominates them, never the platform's `<iid>-…` form:

```
feat/…   fix/…   chore/…   refactor/…   docs/…   security/…   build/…   ci/…
```

Branch off the integration branch (`staging`). Link issues with `(#N)` in the MR title.

## The flow

```
feat/…  fix/…  chore/…                    branch off staging
      │
      │  MR  ── six scoped labels + assign yourself ──┐
      ▼                                               │
  staging  ─────────── auto-deploy ──────────────▶  staging env
      │
      │  promotion MR   🆕 bump(update): <semver>   (flat changelog · NO labels · NO assignee)
      ▼
   main  ───────────── tag <semver> ──────────────▶  prod env
                bare semver · no `v` prefix · feat=minor  fix/chore/docs=patch  breaking=major
```

Never push directly to `staging` or `main`. Both move only by merge request, no exceptions, not
even for a clean fast-forward. Merging to `main` deploys nothing; it marks what is ready. Only a
tag ships production.

Before `glab mr create`, `git checkout` the exact source branch. Never loop mr-create across
branch names: it can force-push local HEAD onto every `--source-branch`.

## Commits

```
<emoji> <type>(<scope>): <short description>

- detail
- detail
```

First line is the major change; bullets are details. All lowercase except formal words and tech
terms (Ansible, TypeScript, LLM, PhaseContext). Group by major change: never one big dump.

**The body is where the "why" lives**: rationale, tradeoffs, gotchas, bug context. Not the code.
A comment explaining why belongs in the commit message instead.

| emoji | type | when |
|---|---|---|
| ✨ | `feat` | new feature or capability |
| 🐛 | `fix` | bug fix |
| ♻️ | `refactor` | restructuring without behaviour change |
| 🎨 | `style` | formatting, whitespace, prettier |
| 🔧 | `chore` | tooling, config, build scripts |
| ✅ | `test` | adding or updating tests |
| ⏪ | `revert` | reverting a previous commit |
| 🚀 | `perf` | performance |
| 📝 | `docs` | documentation only |
| 🔒 | `security` | security-related |
| 🗑️ | `remove` | deleting code or files |
| 🏗️ | `build` | build system or dependencies |
| 👷 | `ci` | CI/CD pipeline |
| 🆕 | `bump` | promotion MR only |

Never include `Co-Authored-By: Claude` or any AI attribution, in commits or files.

## Merge requests

Every MR description is **structured, not prose**: a summary table, the changes, a walkthrough
when the change earns one, a collapsible file list, and what was verified. Templates to copy,
with the commands that build them, are in `references/mr-templates.md`. Read it before writing one.

Three traps worth naming, because they look fine locally and render badly on the platform:

- **tables need real headers.** `| | |` produces an empty header row. Use `| Field | Value |`.
- **no em dashes.** Use a comma, a colon, or parentheses.
- **technical terms go in backticks.** Identifiers, table and column names, enum values,
  environment variables, header names, endpoints, status codes and file paths are `inline code`,
  never bare prose. Anything multi-line (SQL, config, command output) goes in a fenced block.
- **sentence case in an MR body.** Every bullet and every table value opens with a capital and
  reads as a sentence. Where a line would otherwise begin with an identifier, rephrase so it does
  not: "Caps `params` at 2048 bytes", never "`params` is capped". Identifiers keep their own
  casing wherever they appear (`occurred_at`, never `Occurred_at`).

Note this differs from the commit rule on purpose. **Commit subjects stay lowercase**; MR bodies
are the formal artefact a reviewer reads, and they are sentence case.

The two kinds differ by **scope**, not by change type. A change MR is detailed about one change
and carries labels. A promotion MR is broad, covers everything in the release, and carries none.

The shape, both kinds:

| Section | Change MR (to `staging`) | Promotion MR (to `main`) |
|---|---|---|
| Title | Plain formal English, sentence case, naming what the branch delivers | `🆕 bump(update): <semver>` |
| Summary table | type, area, risk, migrations, deploy notes, `Closes`, `Related` | version, bump, commits, diff, migrations, behaviour changes, `Promotes`, `Closes` |
| Body | grouped bullets | one flat changelog, uncategorised |
| Diagram | when data crosses more than two components | same |
| Files | one `<details>` block per category, table inside, files linked at an immutable SHA | same |
| Verification | what was checked, and what was **not** | same |
| Labels | one from each of the six dimensions | none |
| Assignee | yourself | none |

**Link the work.** `Closes #N` in the description closes the issue on merge: an MR that resolves
an issue and does not say so leaves it open forever. `Related` for issues it touches without
finishing. `Promotes`/`Related` for the MRs a promotion carries, so a release is traceable to the
reviews behind it. If there is genuinely no issue, write `n/a`; never drop the row.

**Say what you did not verify.** A verification list with an unchecked box and a reason is worth
more than one with everything ticked: it tells a reviewer where to look.

**Facts over adjectives.** "70 insertions, 0 deletions, no field removed" beats "small, safe
change". Name the thing that could break and why it does not.

### Labels

One label from every scoped dimension: **six total**: and assign yourself. Fetch the project's
labels first (`glab label list`); the taxonomy is identical across repos but confirm before
assuming.

```
area::      api · backend · database · frontend · infra · mobile
effort::    xs (<1h) · s (<1d) · m (1-3d) · l (3-5d) · xl (needs breaking down)
priority::  critical (drop everything) · high · medium · low
severity::  1-blocker · 2-major · 3-minor · 4-trivial
status::    triage · todo · in-progress · in-review · blocked · done
type::      bug · chore · docs · enhancement · feature
```

`status::` is always `status::done` on a staging MR. The `type::` mirrors the branch's commit type
and drives the promotion bump: `type::feature`→minor, `type::bug`/`chore`/`docs`→patch,
breaking→major.

```sh
glab mr create --source-branch <branch> --target-branch staging \
  --title "…" --description "$(cat /tmp/mr.md)" \
  --label "area::api,type::feature,priority::medium,severity::3-minor,effort::m,status::done" \
  --assignee <you> --remove-source-branch --yes
```

Write the description to a file first: a heredoc into `--description` mangles tables and mermaid
fences often enough to be worth avoiding.

Promotion MRs take **no labels and no assignee**. Derive the changelog from
`git log --oneline --no-merges origin/main..origin/staging` so nothing is missed.

## Issues

A board, not a bug tracker. Most issues are a todo, so the format is light: a heavy one gets
skipped, and an empty issue is worse than a terse one. Full shape in
`references/issue-templates.md`.

Titles are plain formal English, sentence case, stating the outcome. **No emoji, no type prefix,
no scope in parentheses**: that grammar is for commits only. The type is the `type::` label and
the area is `area::`, so a title repeating them is duplicated data that can disagree with itself.

```
One link that sends each device to its own store
Scans fail past 55 seconds on mobile uploads
```

State the outcome, not the activity. "Scans fail past 55 seconds" says what is wrong and
therefore what fixed means; "investigate scan failures" says only that someone will look, and is
still true once the work is done.

Body is `What` and `Acceptance`, plus `Context` when there is any, plus `Reproduction` and
`Environment` for a `type::bug`. `Environment` carries a `Regression` row naming the release that
introduced it, which is cheap here because releases are tags.

`status::` tracks where the work is, and merged to `staging` is not shipped: a card stays
`status::in-review` from the MR opening until the release tags. Nothing moves it automatically,
so it is one command alongside the MR:

```sh
glab issue update 42 --label "status::in-review" --unlabel "status::in-progress"
```

The closing end is automatic. `main` is the default branch, so only the promotion MR's
`Closes #N` fires; a `Closes` on a change MR targeting `staging` links and never closes, which is
why change MRs use `Related`.

On open, apply `type::`, `area::`, `status::` and `priority::`. `severity::` is required on a
`type::bug`. `effort::` is optional and should be left blank rather than guessed.

`priority::` is required because it is a judgement rather than data: an unprioritised issue loses
every ranking it is not in, silently. `severity::` is a separate axis, so a trivial-looking
cosmetic bug can still be `priority::high`.

Not every MR has an issue. Small fixes go straight to a branch and the MR says `Closes | n/a`.

## Tags

Bare semver, lightweight, no `-m`, no annotation, no `v` prefix. CI matches
`^[0-9]+\.[0-9]+\.[0-9]+$`, so `v1.0.50` matches nothing and silently does nothing.

```sh
git checkout main && git pull --ff-only origin main
git rev-parse main            # must equal:
git ls-remote origin refs/heads/main
git ls-remote --tags origin   # confirm the version is free
git tag 1.2.0 && git push origin 1.2.0
```

`--ff-only` so a diverged local branch fails loudly instead of tagging a surprise merge commit.
`git status` reports on your last fetch, not the server: merging an MR in the web UI never
reaches your machine, so always re-sync before tagging.

Roll back by re-running the previous tag's pipeline, not by reverting commits.

## CI/CD

```
test ───▶ build ───▶ migrate ───▶ deploy ───▶ verify
 │          │           │            │           └ smoke/health; confirm the new version rolled
 │          │           │            └ rolling / blue-green
 │          │           └ additive migrations for THAT env's db: never migrate a shared db by hand
 │          └ runs the project build script so prebuild/predev asset steps fire
 └ typecheck + lint + unit
```

Confirm before any merge or push. Ship through the pipeline: never hand-deploy around it. After
merging, verify the pipeline passed **and that the new version actually rolled**: check the
deployed image tag, not the pipeline's green tick.

Migrations must be additive: new columns need a default, no destructive changes, 3NF. Generate a
migration file, never `db push`, and check its revision does not collide with what is already on
the integration branch.

Secrets come from the platform, never the repo; they are inert until pods roll.

## Checklist before opening any MR

- [ ] branch name mirrors the commit type
- [ ] commits grouped by major change, "why" in the body, no AI attribution
- [ ] typecheck and lint pass (`npx tsc --noEmit`, `ruff check`, `python -m py_compile`)
- [ ] description follows `references/mr-templates.md`: summary table, changes, file table, verification
- [ ] `Risk` names what could break, `Rollback` says what to do if it is wrong
- [ ] a `fix/` branch carries `Root cause`; issues follow `references/issue-templates.md`
- [ ] `Closes #N` present, or the row says `-` because no issue exists
- [ ] `Related` / `Promotes` link the MRs this builds on
- [ ] verification list states what was **not** checked
- [ ] six scoped labels + assignee (feature→staging only)
- [ ] promotion MRs: `🆕 bump(update): <semver>`, flat changelog, no labels, no assignee
- [ ] the version is free and the bump matches the `type::`
