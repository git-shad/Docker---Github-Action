---
title: "Jobs Steps and Actions"
subject: "GitHub Actions"
level: fundamentals
tags:
  - github-actions
  - fundamentals
---

# Jobs Steps and Actions

Create a dependency between independent runner jobs.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Create a dependency between independent runner jobs. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- What is the difference between a job, a step, and an action?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[03 - GitHub Actions Fundamentals/02 - GitHub Actions Workflow|GitHub Actions Workflow]].

## Introduction, purpose, and how it works

A job is a scheduling unit with its own runner environment. Steps execute sequentially within that environment and share its filesystem. An action is packaged automation invoked by uses. Jobs normally run independently unless needs creates a dependency. Files do not automatically cross job boundaries; artifacts carry files, outputs carry small values, and caches accelerate replaceable dependency downloads.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `needs: check` | Declare a required job | Use for gating; spell the job ID exactly. |
| `uses: OWNER/REPO@SHA` | Invoke a packaged action | Use a reviewed full commit SHA; tags can move. |

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
  check:
    runs-on: ubuntu-24.04
    steps:
      - run: printf "check passed\n"
  package:
    needs: check
    runs-on: ubuntu-24.04
    steps:
      - run: printf "package gate opened\n"
```

## Step-by-step walkthrough

1. In a practice workflow file, define the two jobs shown. Both are harmless shell demonstrations.

2. check completes its steps and records success or failure. package declares needs: check and is normally skipped when check fails.

3. To test gating in the practice repository, temporarily change the first command to `exit 1`. Observe that the second job does not run.

4. Restore the successful command. If later moving compiled files across jobs, explicitly upload/download an artifact; needs alone does not transfer the workspace.

## Expected result

The job graph shows check before package. The failure experiment skips package.

## Practice

Add a third independent greeting job. Success: explain why it can run concurrently with check.

## Common errors and troubleshooting

Unknown needs target: use the exact job ID. Missing file in a downstream job: the jobs have separate workspaces. continue-on-error can weaken a gate and should not hide mandatory test failures.

## Knowledge check

1. What owns a runner?
2. What shares a workspace?
3. What is an action?
4. Does needs copy files?
5. What happens after a required job fails?

> [!question]- Answers
> 1. A job.
> 2. Steps within a job.
> 3. Reusable packaged automation.
> 4. No.
> 5. Dependent jobs are normally skipped.

## Cleanup

No containers or deployed resources are created. Restore the intentional failure before using the practice workflow for other lessons.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[03 - GitHub Actions Fundamentals/02 - GitHub Actions Workflow|GitHub Actions Workflow]]
- [[03 - GitHub Actions Fundamentals/04 - GitHub Runners|Next in the roadmap: GitHub Runners]]

## Official references

- [Jobs official documentation](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-jobs)
- [Security official documentation](https://docs.github.com/en/actions/reference/security/secure-use)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[03 - GitHub Actions Fundamentals/02 - GitHub Actions Workflow\|GitHub Actions Workflow]] | [[03 - GitHub Actions Fundamentals/04 - GitHub Runners\|GitHub Runners]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
