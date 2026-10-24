---
title: "Secrets and Variables"
subject: "GitHub Actions"
level: fundamentals
tags:
  - github-actions
  - fundamentals
---

# Secrets and Variables

Pass configuration to a process without interpolating it into shell source.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Pass configuration to a process without interpolating it into shell source. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- What is the difference between GitHub Actions secrets and variables?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[03 - GitHub Actions Fundamentals/06 - Uses vs Run|Uses vs Run]].

## Introduction, purpose, and how it works

Variables hold ordinary configuration such as a region or public URL. Secrets hold sensitive values such as a credential. Both may have repository, organization, or environment scope, but access depends on event and policy. Masking is a log safeguard, not a guarantee: transformed values may leak, and secrets should never be printed. Environment protections are useful for deployment credentials.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `${{ vars.NAME }}` | Read ordinary configuration | Use for non-sensitive values. |
| `${{ secrets.NAME }}` | Read a secret in an allowed context | Use only in trusted jobs; never echo it. |
| `env:` | Pass strings to processes | Use quoted shell references; env itself does not encrypt logs. |

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
  validate-config:
    runs-on: ubuntu-24.04
    env:
      SERVICE_URL: ${{ vars.SERVICE_URL }}
      API_TOKEN: ${{ secrets.API_TOKEN }}
    steps:
      - name: Check presence only
        run: |
          test -n "$SERVICE_URL"
          test -n "$API_TOKEN"
          printf "Required settings are present\n"
```

## Step-by-step walkthrough

1. For a practice repository, configure a harmless SERVICE_URL variable and a dummy API_TOKEN secret in Settings, Secrets and variables, Actions. Do not use production credentials in a learning repo.

2. The expressions are evaluated into process environment values. The script quotes shell variables rather than embedding event-controlled expressions into Bash source.

3. The tests check presence without revealing contents. Missing secret references can resolve to empty strings, so fail explicitly before operations that need them.

4. Run manually. Do not grant secrets to untrusted pull-request code, third-party actions without review, or frontend bundles.

## Expected result

The job confirms presence without displaying either value. This does not test authentication to any external service.

## Practice

Remove the dummy secret and rerun. Success: the presence check fails before an API operation could occur.

## Common errors and troubleshooting

Unexpected empty value: check scope, environment attachment, event restrictions, and exact name. If a secret leaks, revoke or rotate it and remove exposed logs; hiding future output is insufficient.

## Knowledge check

1. What belongs in vars?
2. Are masked secrets safe to print?
3. What can an unset secret become?
4. Why use env and quotes?
5. Can frontend build variables carry private tokens?

> [!question]- Answers
> 1. Non-sensitive configuration.
> 2. No.
> 3. An empty string.
> 4. Keep values as data instead of shell source.
> 5. No.

## Cleanup

Delete the dummy practice secret when finished. No token has been sent to an external API by this example.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[03 - GitHub Actions Fundamentals/06 - Uses vs Run|Uses vs Run]]
- [[03 - GitHub Actions Fundamentals/08 - Troubleshooting Workflows|Next in the roadmap: Troubleshooting Workflows]]

## Official references

- [Secrets official documentation](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets)
- [Security official documentation](https://docs.github.com/en/actions/reference/security/secure-use)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[03 - GitHub Actions Fundamentals/06 - Uses vs Run\|Uses vs Run]] | [[03 - GitHub Actions Fundamentals/08 - Troubleshooting Workflows\|Troubleshooting Workflows]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
