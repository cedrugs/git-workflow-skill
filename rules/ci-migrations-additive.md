---
title: Migrations Are Additive
impact: CRITICAL
impactDescription: a destructive migration breaks the running release mid-deploy
tags: ci, database, migrations
---

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
