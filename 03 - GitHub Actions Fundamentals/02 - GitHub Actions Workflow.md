---
title: "GitHub Actions Workflow"
subject: "GitHub Actions"
level: fundamentals
tags:
  - github-actions
  - fundamentals
---

# GitHub Actions Workflow

Read a minimal workflow and identify its scope and file location.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Read a minimal workflow and identify its scope and file location. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- What belongs in a GitHub Actions workflow YAML file?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[03 - GitHub Actions Fundamentals/01 - Introduction to CI CD|Introduction to CI CD]].

## Introduction, purpose, and how it works

A workflow file is a YAML document under .github/workflows in a repository. YAML mappings use key/value pairs; sequences use dashes. Indentation changes meaning, so use spaces consistently. GitHub evaluates workflow structure before allocating a runner. A display name is different from an internal job identifier used by dependencies.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `jobs:` | Define units of runner work | Use job IDs as mapping keys; do not confuse IDs with display names. |
| `steps:` | Define ordered actions or commands | Use beneath a job; individual steps do not each get a separate VM. |
| `timeout-minutes: 5` | Limit job duration | Use to bound hung checks. |

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
  inspect:
    name: Inspect runtime
    runs-on: ubuntu-24.04
    timeout-minutes: 5
    steps:
      - name: Show shell and directory
        run: |
          bash --version
          pwd
```

## Step-by-step walkthrough

1. Read this as .github/workflows/inspect.yml in a practice repository. It requires no project source or credentials.

2. name labels the workflow in Actions; on chooses events; permissions constrains the built-in token. This example grants contents: read though it does not write anything.

3. jobs maps inspect to a job configuration. runs-on selects the machine; timeout-minutes stops excessive runtime.

4. steps is ordered. The YAML pipe introduces a multiline script; Bash runs both commands in the same step. On a hosted Linux runner an explicit Bash shell uses failure-sensitive execution, but scripts must still return meaningful exit codes.

5. If manually trying the file, dispatch it from the default branch and inspect each log section.

## Expected result

The run shows a Bash version and runner workspace path; those values are illustrative and can change.

## Practice

Add a second named run step printing a fixed word. Success: both steps appear separately in order.

## Common errors and troubleshooting

Invalid workflow file before a runner starts is a YAML or schema issue. Tabs, misplaced steps, and incorrectly nested runs-on are common causes. A generic YAML parser may interpret on as a boolean under YAML 1.1; use an Actions-aware validator.

## Knowledge check

1. Where is the file?
2. What does jobs contain?
3. What denotes a sequence item?
4. What does | mean?
5. Is the runner environment permanent?

> [!question]- Answers
> 1. .github/workflows.
> 2. Named job definitions.
> 3. A dash.
> 4. A multiline YAML scalar.
> 5. No guarantee; hosted environments evolve.

## Cleanup

No external resources exist. Disable or remove only your practice workflow if no longer needed.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[03 - GitHub Actions Fundamentals/01 - Introduction to CI CD|Introduction to CI CD]]
- [[03 - GitHub Actions Fundamentals/03 - Jobs Steps and Actions|Next in the roadmap: Jobs Steps and Actions]]

## Official references

- [Workflow official documentation](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[03 - GitHub Actions Fundamentals/01 - Introduction to CI CD\|Introduction to CI CD]] | [[03 - GitHub Actions Fundamentals/03 - Jobs Steps and Actions\|Jobs Steps and Actions]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
