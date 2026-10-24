---
title: "Troubleshooting Workflows"
subject: "GitHub Actions"
level: fundamentals
tags:
  - github-actions
  - fundamentals
---

# Troubleshooting Workflows

Locate the first failing command and distinguish runner, configuration, and application failures.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Locate the first failing command and distinguish runner, configuration, and application failures. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I find the real cause of a failed workflow?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[03 - GitHub Actions Fundamentals/07 - Secrets and Variables|Secrets and Variables]].

## Introduction, purpose, and how it works

A workflow can fail before scheduling, during runner setup, or inside an application command. Later red messages often follow an earlier root cause. Start with the earliest meaningful error, command exit code, and exact commit. A rerun can reveal a transient network issue but cannot fix reproducible source or lock-file problems. Preserve diagnostic logs without exposing secrets.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `if: failure()` | Run failure diagnostics | Use targeted bounded commands; do not conceal the failing status. |
| `exit N` | Set a process result | Zero means success; nonzero should preserve a required gate. |

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
  diagnose:
    runs-on: ubuntu-24.04
    steps:
      - name: Deliberate failure
        run: exit 7
      - name: Ordinary next step
        run: printf "Skipped after failure\n"
      - name: Bounded diagnostics
        if: failure()
        run: |
          pwd
          df -h .
```

## Step-by-step walkthrough

1. Try only in a practice workflow. This intentionally fails with exit code 7 and does not validate an application.

2. The next ordinary step is skipped because prior success is the default condition.

3. failure() enables a targeted diagnostic step after failure. Disk information can reveal storage exhaustion without dumping secret-bearing environment values.

4. Open Actions, select the exact run and failed job, expand the first failing step, and read preceding lines as well as the final error. Record commit, event, runner OS, and tool versions.

## Expected result

The run remains failed while the bounded diagnostics still execute.

## Practice

Replace exit 7 with a successful printf. Success: the diagnostic step skips and the ordinary next step runs.

## Common errors and troubleshooting

YAML invalid: fix structure before investigating application commands. Exit 127 typically means missing command. Exit 137 can indicate forced termination, including OOM; inspect runner and container evidence before concluding.

## Knowledge check

1. Where do you start?
2. Does a rerun fix bad source?
3. What does failure() select?
4. What does exit 7 mean here?
5. Should diagnostics print all env?

> [!question]- Answers
> 1. The earliest meaningful failure.
> 2. No.
> 3. Steps after a previous failure.
> 4. An intentional nonzero result.
> 5. No.

## Cleanup

Restore the intentional failure before reusing this workflow. Remove any uploaded logs that inadvertently contain sensitive data and rotate exposed credentials.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[03 - GitHub Actions Fundamentals/07 - Secrets and Variables|Secrets and Variables]]
- [[04 - Docker and GitHub Actions/01 - Docker in GitHub Actions|Next in the roadmap: Docker in GitHub Actions]]

## Official references

- [Logs official documentation](https://docs.github.com/en/actions/how-tos/monitor-workflows/use-workflow-run-logs)
- [Workflow official documentation](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[03 - GitHub Actions Fundamentals/07 - Secrets and Variables\|Secrets and Variables]] | [[04 - Docker and GitHub Actions/01 - Docker in GitHub Actions\|Docker in GitHub Actions]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
