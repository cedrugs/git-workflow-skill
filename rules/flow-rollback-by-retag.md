---
title: Roll Back by Re-running the Previous Tag
impact: HIGH
impactDescription: a revert commit creates a new release path and a divergent history
tags: flow, release, rollback
---

## Roll Back by Re-running the Previous Tag

The previous tag is a known-good, already-built artefact. Re-run its pipeline. Reverting commits
to undo a release adds history that has to be un-reverted later and changes what `main` means.

A migration is the exception worth naming: re-running an older tag does not undo a schema change,
so an MR whose migration cannot be reversed says so in its `Rollback` row.

Reference: `SKILL.md`, Tags.
