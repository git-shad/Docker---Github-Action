---
title: "GitHub Actions YAML Reference"
subject: "GitHub Actions"
level: reference
tags:
  - github-actions
  - reference
---

# GitHub Actions YAML Reference

Find a workflow keyword’s scope and common failure mode.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Find a workflow keyword’s scope and common failure mode. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- What are the core GitHub Actions workflow keys?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[03 - GitHub Actions Fundamentals/02 - GitHub Actions Workflow|GitHub Actions Workflow]], [[03 - GitHub Actions Fundamentals/03 - Jobs Steps and Actions|Jobs Steps and Actions]].

## Introduction, purpose, and how it works

GitHub evaluates workflow expressions in defined contexts and executes shell commands on runners. `${{ ... }}` is GitHub expression syntax; `$NAME` is shell expansion on Linux. A workflow job, reusable-workflow call, and action step have different allowed keys. A YAML file can parse successfully yet violate GitHub’s workflow schema.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `name` | Label a workflow or step | Use descriptive display names; job dependency references use IDs. |
| `on` | Choose trigger events | Use filters deliberately, especially with required checks. |
| `permissions` | Limit GITHUB_TOKEN scopes | Use contents: read by default and elevate only necessary jobs. |
| `runs-on` | Choose the runner | Use an actual supported label. |
| `needs` | Declare prerequisite job IDs | Use to enforce required checks. |
| `steps` | Order work within one job | Use run or uses per step. |
| `env` | Supply process variables | Treat event values as data and quote shell references. |
| `if` | Conditionally run a job or step | Avoid always() on real deployment gates. |
| `strategy.matrix` | Generate job combinations | Use bounded supported combinations. |
| `environment` | Attach deployment policy | Configure its rules in repository settings. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```yaml
name: Learning example
on:
  workflow_dispatch:
permissions:
  contents: read
jobs:
  first:
    runs-on: ubuntu-24.04
    steps:
      - id: value
        run: printf "status=ready\n" >> "$GITHUB_OUTPUT"
      - env:
          STATUS: ${{ steps.value.outputs.status }}
        run: test "$STATUS" = ready
```

## Step-by-step walkthrough

1. Read this as .github/workflows/reference.yml in a practice repository. No checkout is needed for this shell-only example.

2. id names the first step so later expressions can access its outputs. GITHUB_OUTPUT is a runner-provided file; writing status=ready records one output.

3. The next step maps the expression into an environment variable and tests it using Bash. Avoid placing untrusted event text directly into script source.

4. To cross job boundaries, expose a job output and read it through needs in a downstream job; a step output alone is scoped to that job.

## Expected result

The second step verifies the first step’s output and the workflow succeeds.

## Practice

Rename the output key consistently in writer and reader. Success: preserve the check without leaking scope assumptions.

## Common errors and troubleshooting

Empty output: check step id, output name, and whether the producer ran. Incorrect permissions: set minimal job-specific scopes. Expressions unavailable in a location: consult the context availability documentation.

## Knowledge check

1. What evaluates expressions?
2. What expands $NAME?
3. What names a step for references?
4. What records outputs?
5. Do step outputs automatically cross jobs?

> [!question]- Answers
> 1. GitHub Actions.
> 2. The shell.
> 3. id.
> 4. GITHUB_OUTPUT.
> 5. No.

## Cleanup

No external resources are created. Do not use output files as a way to publish credentials into logs or downstream untrusted jobs.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[03 - GitHub Actions Fundamentals/02 - GitHub Actions Workflow|GitHub Actions Workflow]]
- [[03 - GitHub Actions Fundamentals/03 - Jobs Steps and Actions|Jobs Steps and Actions]]
- [[07 - References/05 - DevOps Glossary|Next in the roadmap: DevOps Glossary]]

## Official references

- [Workflow official documentation](https://docs.github.com/en/actions/reference/workflows-and-actions/workflow-syntax)
- [Jobs official documentation](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-jobs)
- [Security official documentation](https://docs.github.com/en/actions/reference/security/secure-use)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[07 - References/03 - Docker Compose YAML Reference\|Docker Compose YAML Reference]] | [[07 - References/05 - DevOps Glossary\|DevOps Glossary]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
