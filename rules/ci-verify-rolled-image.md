---
title: A Green Pipeline Is Not a Deployed Version
impact: CRITICAL
impactDescription: reporting a rollout that never happened
tags: ci, deploy, verification
---

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
