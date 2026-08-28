# Git Workflow Skill

![Claude Code](https://img.shields.io/badge/Claude%20Code-Skill-black?style=flat)
![Markdown](https://img.shields.io/badge/Markdown-blue?style=flat)
![GitLab](https://img.shields.io/badge/GitLab-FC6D26?style=flat)
![License](https://img.shields.io/badge/license-MIT-green?style=flat)

Version control conventions for AI agents: commit grammar, branch flow, merge request and issue formats, scoped labels, semantic release bumps, and the CI/CD pipeline.

## Tech Stack

- **Format:** Claude Code Agent Skill
- **Language:** Markdown with YAML frontmatter
- **Targets:** GitLab (`glab`), GitHub (`gh`), plain `git`
- **Rules:** 30 across five sections
- **License:** MIT

## Project Structure

```sh
git-workflow/
├── SKILL.md                        Entry point, loaded when a task touches git
├── AGENTS.md                       Full reference, every rule inlined. Generated
├── README.md                       This file
├── CONTRIBUTING.md                 How to add or change a rule
├── metadata.json                   Version, abstract, maintainer, references
├── references/
│   ├── mr-templates.md             Change MR and Promotion MR blocks
│   └── issue-templates.md          Issue title, body, labels, linking
└── rules/
    ├── _sections.md                Section ordering and impact levels
    ├── _template.md                Shape of a new rule file
    └── *.md                        One file per rule
```

## Setup Instructions

Clone into the Claude Code skills directory:

```sh
git clone https://github.com/cedrugs/git-workflow-skill.git ~/.claude/skills/git-workflow
```

The skill loads on its own when a task involves `git`, `glab`, `gh`, a merge request, an issue, a tag or a deploy pipeline. No configuration.

To point a project's own instructions at it, reference the skill by name and keep only the non-negotiables inline:

```md
All of it lives in the `git-workflow` skill. Load it before writing a commit
message, opening or reviewing an MR, or cutting a tag.
```

## Key Features

### Completed

- [x] Commit grammar with an emoji and type table, and the rule that the reasoning belongs in the body rather than a code comment
- [x] Branch to staging to promotion to tag flow, with protected branches moving only by merge request
- [x] Change MR and Promotion MR templates: summary table, walkthrough, collapsible file list, verification
- [x] Issue format sized for a board rather than a bug tracker
- [x] Six-dimension scoped label taxonomy, with rules for which are required when
- [x] Semantic version bumps derived from the highest change type in a release
- [x] Thirty rules, each naming the trap it prevents rather than only the rule
- [x] Generated `AGENTS.md` so the full reference cannot drift from the rule files

### In Progress

- [ ] A generator script so `AGENTS.md` is rebuilt by command rather than by hand
- [ ] Worked examples drawn from public repositories
- [ ] Platform notes for GitHub, where issue closing and label semantics differ from GitLab

## Design System

The skill applies its own formatting rules to itself:

- **Tables:** real headers, since an empty header row renders as a broken table
- **Dashes:** no em dashes, use a comma, a colon, or parentheses
- **Technical terms:** identifiers, columns, enum values and endpoints in backticks
- **Case:** sentence case in merge request and issue bodies, lowercase in commit subjects
- **Fences:** an example containing a nested fence uses a four-backtick outer fence

## Deployment

`AGENTS.md` is generated from `rules/`, so a change to a rule needs the reference rebuilt before it is committed. See `CONTRIBUTING.md`.

- **Distribution:** clone into `~/.claude/skills/`
- **Versioning:** `metadata.json`, semantic
- **Updates:** `git pull` in the skill directory

## Maintainer

- **Samuel Cedric** ([@cedrugs](https://github.com/cedrugs))

## License

MIT. See `LICENSE`.
