---
title: "GitHub Runners"
subject: "GitHub Actions"
level: fundamentals
tags:
  - github-actions
  - fundamentals
---

# GitHub Runners

Inspect a hosted runner and understand the trust boundary.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Inspect a hosted runner and understand the trust boundary. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- Should I use a GitHub-hosted runner or a self-hosted runner?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[03 - GitHub Actions Fundamentals/03 - Jobs Steps and Actions|Jobs Steps and Actions]].

## Introduction, purpose, and how it works

A runner is the machine or process that executes job instructions. Standard GitHub-hosted Linux jobs generally receive fresh virtual machines; do not rely on files from earlier jobs. Self-hosted runners are operated by you and can retain files, credentials, and malicious changes between jobs. Untrusted pull-request code must not gain access to privileged internal networks or persistent deployment runners.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `runs-on: ubuntu-24.04` | Request a hosted Linux runner | Use a known OS label and inspect installed software. |
| `runs-on: [self-hosted, linux]` | Select matching private labels | Use only with an explicit runner security and maintenance plan. |

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
  runtime:
    runs-on: ubuntu-24.04
    timeout-minutes: 5
    steps:
      - run: |
          uname -s
          docker version
          docker compose version
```

## Step-by-step walkthrough

1. Use a practice repository. This example assumes a standard GitHub-hosted Ubuntu runner; inspect its current installed-software listing before depending on bundled tooling.

2. runs-on requests an OS label, not a Docker image. Pinning ubuntu-24.04 reduces OS-family drift compared with ubuntu-latest but does not freeze all packages.

3. The script prints kernel family and Docker/Compose versions. It does not launch a container.

4. Document the observed versions in your own run. For stable toolchains, install a reviewed version explicitly or use a versioned job container where appropriate.

## Expected result

The Linux job logs show installed tool versions. A missing tool should fail rather than silently selecting a different runtime.

## Practice

Compare the hosted runner environment with your local machine. Success: list two differences that can affect reproducibility.

## Common errors and troubleshooting

Queued self-hosted job: check runner labels, connectivity, and whether it is online. Docker commands in container jobs may lack daemon access. Do not expose the host Docker socket merely to bypass an unclear permission error.

## Knowledge check

1. Is runs-on a container image?
2. Does an OS label freeze every tool?
3. Who patches a self-hosted runner?
4. Can untrusted PR code be dangerous there?
5. Should jobs rely on prior local files?

> [!question]- Answers
> 1. No.
> 2. No.
> 3. Its operator.
> 4. Yes.
> 5. No.

## Cleanup

Hosted VM cleanup is managed by GitHub. Self-hosted operators must provide workspace cleanup, credential removal, isolation, and lifecycle policy.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[03 - GitHub Actions Fundamentals/03 - Jobs Steps and Actions|Jobs Steps and Actions]]
- [[03 - GitHub Actions Fundamentals/05 - Workflow Events|Next in the roadmap: Workflow Events]]

## Official references

- [Runner official documentation](https://docs.github.com/en/actions/how-tos/write-workflows/choose-where-workflows-run/choose-the-runner-for-a-job)
- [Security official documentation](https://docs.github.com/en/actions/reference/security/secure-use)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[03 - GitHub Actions Fundamentals/03 - Jobs Steps and Actions\|Jobs Steps and Actions]] | [[03 - GitHub Actions Fundamentals/05 - Workflow Events\|Workflow Events]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
