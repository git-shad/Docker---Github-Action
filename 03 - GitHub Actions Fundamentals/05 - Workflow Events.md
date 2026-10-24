---
title: "Workflow Events"
subject: "GitHub Actions"
level: fundamentals
tags:
  - github-actions
  - fundamentals
---

# Workflow Events

Filter workflow events without confusing source and target branches.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Filter workflow events without confusing source and target branches. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I run a workflow only for pull requests to main?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[03 - GitHub Actions Fundamentals/04 - GitHub Runners|GitHub Runners]].

## Introduction, purpose, and how it works

An event is something GitHub observes, such as a push, pull request, or manual request. Filters narrow the eligible branch or changed paths. For pull_request, branches refers to the destination branch. A branch filter and a path filter both need to match when both are configured. Use pull_request for ordinary untrusted contribution tests; pull_request_target has a more privileged context and is not a shortcut for running fork code with secrets.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `on.pull_request` | Subscribe to PR activity | Use for contribution checks. |
| `branches: [main]` | Filter the destination branch | Use for targeted checks; it is not the feature-branch name. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** CI demonstration in a practice repository. The walkthrough identifies filenames and the machine where each operation belongs.

```yaml
name: Pull request check
on:
  pull_request:
    branches: [main]
permissions:
  contents: read
jobs:
  check:
    runs-on: ubuntu-24.04
    steps:
      - run: printf "Checking a pull request to main\n"
```

## Step-by-step walkthrough

1. In a practice repository, save this as .github/workflows/pr.yml. Adapt main only if the actual target branch differs.

2. The event is pull_request; opening or updating an eligible request creates a run under the event’s default activity types.

3. The branch list matches the base branch, not the author’s feature branch. The script has no dependency on repository files.

4. Open a practice pull request targeting main and inspect its Checks tab. Fork-related approval and repository policy may delay execution.

## Expected result

Eligible pull requests receive a check; ordinary pushes without an associated PR event do not trigger this workflow.

## Practice

Write a separate push trigger filtered to a sandbox branch. Success: predict which event runs each workflow before testing.

## Common errors and troubleshooting

No run: inspect event type, branch filters, Actions policy, and fork approval. Skipping required workflows through path filters can leave required checks pending; design branch-protection checks deliberately.

## Knowledge check

1. What does branches match on PRs?
2. Do both branch and path filters need matching?
3. Are fork secrets freely available?
4. Is pull_request_target safer for arbitrary checkout?
5. Where do you inspect results?

> [!question]- Answers
> 1. The target branch.
> 2. Yes.
> 3. No.
> 4. No.
> 5. The PR Checks tab or Actions run.

## Cleanup

Close the practice PR when finished. Do not switch to privileged PR events just to bypass secret restrictions.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[03 - GitHub Actions Fundamentals/04 - GitHub Runners|GitHub Runners]]
- [[03 - GitHub Actions Fundamentals/06 - Uses vs Run|Next in the roadmap: Uses vs Run]]

## Official references

- [Events official documentation](https://docs.github.com/en/actions/how-tos/write-workflows/choose-when-workflows-run/trigger-a-workflow)
- [Security official documentation](https://docs.github.com/en/actions/reference/security/secure-use)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[03 - GitHub Actions Fundamentals/04 - GitHub Runners\|GitHub Runners]] | [[03 - GitHub Actions Fundamentals/06 - Uses vs Run\|Uses vs Run]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
