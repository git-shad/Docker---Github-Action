---
title: "Uses vs Run"
subject: "GitHub Actions"
level: fundamentals
tags:
  - github-actions
  - fundamentals
---

# Uses vs Run

Combine a pinned checkout action with a shell command.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Combine a pinned checkout action with a shell command. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- When should I use uses and when should I use run?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[03 - GitHub Actions Fundamentals/05 - Workflow Events|Workflow Events]].

## Introduction, purpose, and how it works

uses delegates to an action, which can accept documented inputs through with. run executes shell text on the runner. A single step cannot contain both. Marketplace popularity is not a security guarantee: review the action source and pin to a full commit SHA. A pin protects the reference from tag movement but still requires code review and maintenance.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `uses: OWNER/REPO@FULL_SHA` | Select reviewed action code | Use a real immutable revision; tags can be moved. |
| `with:` | Pass action inputs | Use action-defined keys; these are not shell environment variables. |
| `run: COMMAND` | Execute shell text | Use environment variables for untrusted values instead of expression interpolation into scripts. |

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
    runs-on: ubuntu-24.04
    steps:
      - name: Check out source
        uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
        with:
          persist-credentials: false
      - name: List tracked paths
        run: git ls-files
```

## Step-by-step walkthrough

1. Use a practice repository with at least one tracked file. This example intentionally pins checkout v4.2.2 rather than presenting a mutable version tag as a security pin. Review upstream updates and organization policy before adopting it.

2. uses downloads the selected action revision; with supplies its persist-credentials input. Setting that false avoids retaining the token in local Git configuration after checkout.

3. run invokes git after checkout has populated the workspace. Without checkout, repository files are not automatically present.

4. When adding another action, resolve its release to a full commit on the official repository and review the source; never paste an invented SHA.

## Expected result

The second step lists tracked repository paths without leaving checkout credentials persisted in Git configuration.

## Practice

Add a second shell step reading one known harmless file. Success: explain why it can use files downloaded by the previous action.

## Common errors and troubleshooting

Unexpected key run: it may share a step with uses. Invalid with input: read that action’s input documentation. Command not found: run needs a tool installed on the runner.

## Knowledge check

1. What invokes reusable automation?
2. What invokes a shell?
3. Can both appear in one step?
4. What does with configure?
5. Does a full SHA replace review?

> [!question]- Answers
> 1. uses.
> 2. run.
> 3. No.
> 4. Action inputs.
> 5. No.

## Cleanup

No external service is created. Do not log Git credentials or dump the entire environment while troubleshooting.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[03 - GitHub Actions Fundamentals/05 - Workflow Events|Workflow Events]]
- [[03 - GitHub Actions Fundamentals/07 - Secrets and Variables|Next in the roadmap: Secrets and Variables]]

## Official references

- [Security official documentation](https://docs.github.com/en/actions/reference/security/secure-use)
- [Workflow official documentation](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax)

- [Official checkout v4.2.2 release and commit](https://github.com/actions/checkout/releases/tag/v4.2.2)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[03 - GitHub Actions Fundamentals/05 - Workflow Events\|Workflow Events]] | [[03 - GitHub Actions Fundamentals/07 - Secrets and Variables\|Secrets and Variables]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
