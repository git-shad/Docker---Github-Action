---
title: "GitHub Actions YAML Examples"
subject: "GitHub Actions"
level: projects
tags:
  - github-actions
  - projects
---

# GitHub Actions YAML Examples

Use a matrix and needs without assuming shared files.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Use a matrix and needs without assuming shared files. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How can I fan out validation and gate a later job?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[04 - Docker and GitHub Actions/07 - React TypeScript Build Validation|React TypeScript Build Validation]], [[03 - GitHub Actions Fundamentals/03 - Jobs Steps and Actions|Jobs Steps and Actions]].

## Introduction, purpose, and how it works

A matrix generates several versions of a job from one definition. It is useful for testing supported runtimes or operating systems. needs lets a later job wait for all required matrix checks. More combinations cost more runner time, so choose a support policy rather than every possible version. Artifacts preserve selected outputs between jobs; dependency caches are replaceable accelerators, not authoritative release artifacts.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `strategy.matrix` | Expand a job into combinations | Use supported versions with clear cost and coverage goals. |
| `fail-fast: false` | Retain diagnostic coverage after failure | Use when finishing other combinations is useful. |

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
  node-check:
    runs-on: ubuntu-24.04
    strategy:
      fail-fast: false
      matrix:
        node: [22, 24]
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
        with:
          persist-credentials: false
      - env:
          NODE_MAJOR: ${{ matrix.node }}
        run: docker run --rm --mount type=bind,src="$GITHUB_WORKSPACE",dst=/app -w /app "node:${NODE_MAJOR}-bookworm-slim" sh -c 'npm ci && npm run typecheck && npm run build'
  accepted:
    needs: node-check
    runs-on: ubuntu-24.04
    steps:
      - run: printf "Both configured Node versions passed\n"
```

## Step-by-step walkthrough

1. Use the React project from React TypeScript Build Validation with its required scripts and lock file. Choose Node versions supported by its package engines; 22 and 24 are explicit teaching targets, not a claim that every dependency supports both.

2. The matrix starts one hosted job for each major version. Each receives its own workspace and Docker daemon.

3. fail-fast false allows remaining combinations to finish after one fails, improving diagnostic coverage. The accepted job still requires all successful matrix jobs.

4. For real artifact transfer, add reviewed pinned upload/download actions and choose retention; never cache credentials, .env, or treat a restored dependency cache as trusted build output.

## Expected result

The run shows two validation jobs and one gate that runs only if both pass.

## Practice

Create a version-specific failing assertion in a sandbox. Success: the other matrix job still finishes but accepted is skipped.

## Common errors and troubleshooting

Matrix jobs conflict on a shared external resource: isolate resources per combination. Missing files in accepted: needs does not transfer workspaces. Cache misses should slow a job, not make it incorrect.

## Knowledge check

1. What creates multiple jobs?
2. What does fail-fast false change?
3. Does accepted get dist automatically?
4. What are caches for?
5. Should secrets go in caches?

> [!question]- Answers
> 1. The matrix.
> 2. Other combinations keep running after a failure.
> 3. No.
> 4. Replaceable performance optimization.
> 5. No.

## Cleanup

Temporary Node containers remove themselves. Hosted job disposal removes build directories; no artifacts are uploaded here.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[04 - Docker and GitHub Actions/07 - React TypeScript Build Validation|React TypeScript Build Validation]]
- [[03 - GitHub Actions Fundamentals/03 - Jobs Steps and Actions|Jobs Steps and Actions]]
- [[06 - Practical Examples/04 - Laravel React MariaDB Architecture|Next in the roadmap: Laravel React MariaDB Architecture]]

## Official references

- [Jobs official documentation](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-jobs)
- [Workflow official documentation](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[06 - Practical Examples/02 - Docker Compose Examples\|Docker Compose Examples]] | [[06 - Practical Examples/04 - Laravel React MariaDB Architecture\|Laravel React MariaDB Architecture]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
