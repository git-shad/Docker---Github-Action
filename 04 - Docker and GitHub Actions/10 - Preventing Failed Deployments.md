---
title: "Preventing Failed Deployments"
subject: "GitHub Actions"
level: projects
tags:
  - github-actions
  - projects
---

# Preventing Failed Deployments

Create a dependency gate and understand its limits.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Create a dependency gate and understand its limits. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I stop a failed validation job from reaching deployment?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[03 - GitHub Actions Fundamentals/03 - Jobs Steps and Actions|Jobs Steps and Actions]].

## Introduction, purpose, and how it works

A deployment gate combines automated evidence and explicit policy. needs blocks a downstream job unless its prerequisites succeed. An environment can add branch restrictions and human approval. A gate cannot guarantee correctness when tests miss a bug; staging checks, monitoring, and rollback remain necessary. A job named deploy does not deploy unless its steps actually change a target.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `needs: verify` | Gate downstream work on success | Use all required validation jobs. |
| `environment: production` | Attach deployment policy | Configure protection in repository settings. |
| `concurrency.group` | Serialize work in a shared group | Use a stable target-specific group; it is not FIFO. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** CI demonstration in a practice repository. The walkthrough identifies filenames and the machine where each operation belongs.

```yaml
name: Learning example
on:
  workflow_dispatch:
permissions:
  contents: read
jobs:
  verify:
    runs-on: ubuntu-24.04
    steps:
      - run: test 2 -eq 2
  release-gate:
    needs: verify
    runs-on: ubuntu-24.04
    environment: production
    concurrency:
      group: production-release
      cancel-in-progress: false
    steps:
      - run: printf "Validation and configured environment gates passed; no deployment performed\n"
```

## Step-by-step walkthrough

1. In a practice repository, configure an environment named production before trying the workflow. Review plan/repository availability for required reviewers. Merely naming the environment does not configure protection.

2. verify provides a tiny deterministic assertion. Replace it with real required test jobs in an application pipeline.

3. release-gate depends on verify and requests the protected environment. Configure deployment branch restrictions and approval policy in repository Settings.

4. The concurrency group prevents simultaneous jobs in that group; it is not a guaranteed FIFO deployment queue. cancel-in-progress false avoids aborting an active deployment.

5. Read the final message literally: this exercise validates gating only. A real deployment must promote the tested digest and verify runtime health.

## Expected result

A failed verify job prevents the gate job; a successful job may wait for configured environment approval.

## Practice

Change the assertion to false. Success: the environment job is skipped without requesting a deployment.

## Common errors and troubleshooting

Approval never appears: check environment rules and feature availability. Deployment despite failure: inspect always(), continue-on-error, and missing needs links. Pending concurrency runs may be superseded; do not assume queue ordering.

## Knowledge check

1. What links checks to release?
2. Does environment alone add reviewers?
3. Why avoid canceling an active deployment?
4. Does the example deploy?
5. What should a real release promote?

> [!question]- Answers
> 1. needs.
> 2. No.
> 3. It may leave partial changes.
> 4. No.
> 5. The tested immutable image digest.

## Cleanup

No deployment occurs. Remove only the practice environment if it is no longer used and contains no needed configuration.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[03 - GitHub Actions Fundamentals/03 - Jobs Steps and Actions|Jobs Steps and Actions]]
- [[05 - Advanced CI CD/01 - Deployment to Ubuntu Server|Next in the roadmap: Deployment to Ubuntu Server]]

## Official references

- [Jobs official documentation](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-jobs)
- [Env official documentation](https://docs.github.com/en/actions/how-tos/deploy/configure-and-manage-deployments/manage-environments)
- [Security official documentation](https://docs.github.com/en/actions/reference/security/secure-use)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[04 - Docker and GitHub Actions/09 - Publishing Docker Images\|Publishing Docker Images]] | [[05 - Advanced CI CD/01 - Deployment to Ubuntu Server\|Deployment to Ubuntu Server]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
