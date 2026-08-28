---
title: Secrets Come From the Platform and Are Inert Until Pods Roll
impact: HIGH
impactDescription: editing a secret appears to work and changes nothing
tags: ci, secrets, deploy
---

## Secrets Come From the Platform and Are Inert Until Pods Roll

Never commit secrets. Application environment is read once at pod start, so editing a secret
changes nothing until the pods cycle. A deploy that "did not pick up" a new value has usually
just not restarted.

Reference: `SKILL.md`, CI/CD.
