# Contributing

How to add or change a rule. Structure and installation are in `README.md`.

## Structure

- `SKILL.md` - the entry point. Concise, loaded automatically when a task touches git, a merge
  request, an issue, a tag or a pipeline.
- `AGENTS.md` - the complete reference, every rule inlined with examples. Generated from
  `rules/`; do not edit by hand.
- `rules/` - one file per rule, 30 across five sections.
  - `_sections.md` - section ordering, impact levels and what each section covers.
  - `_template.md` - the shape a new rule file takes.
- `references/` - the templates a rule points at.
  - `mr-templates.md` - Change MR and Promotion MR, plus the commands that build their tables.
  - `issue-templates.md` - issue title grammar, body, labels and linking.
- `metadata.json` - version, abstract and external references.

## What it covers

| Section | Rules | Impact |
|---|---|---|
| Commits | 4 | HIGH |
| Branches and flow | 6 | CRITICAL |
| Merge requests | 11 | HIGH |
| Issues | 6 | MEDIUM |
| CI/CD and deploys | 3 | CRITICAL |

## The flow it encodes

```
feat/…  fix/…  chore/…                    branch off staging
      |
      |  MR  -- six scoped labels + assign yourself --+
      v                                               |
  staging  ------------ auto-deploy ---------------> staging env
      |
      |  promotion MR   bump(update): <semver>   (flat changelog, no labels)
      v
   main  -------------- tag <semver> ------------->  prod env
                bare semver, no v prefix
                feat = minor, fix/chore/docs = patch, breaking = major
```

## Adding a rule

1. Copy `rules/_template.md` to `rules/<section>-<slug>.md`, where `<section>` is one of
   `commit`, `flow`, `mr`, `issue`, `ci`.
2. Fill the frontmatter: `title`, `impact`, `impactDescription`, `tags`.
3. State the trap the rule prevents, not only the rule. A rule that says what to do is a style
   guide; a rule that says what goes wrong when you skip it is one people follow.
4. Give an incorrect and a correct example. Real ones beat invented ones.
5. Regenerate `AGENTS.md`.

## Formatting rules the skill applies to itself

- Tables carry real headers. `| | |` renders as an empty header row.
- No em dashes. Use a comma, a colon, or parentheses.
- Technical terms in backticks; anything multi-line in a fenced block.
- Sentence case in MR and issue bodies. Commit subjects stay lowercase.
- An example block containing a mermaid fence needs a four-backtick outer fence, or the inner
  backticks close it early.
