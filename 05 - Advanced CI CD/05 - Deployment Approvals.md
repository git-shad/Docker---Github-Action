---
title: "Deployment Approvals"
subject: "GitHub Actions"
level: advanced
tags:
  - github-actions
  - advanced
---

# Deployment Approvals

Configure an approval boundary and recognize plan and policy limits.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Configure an approval boundary and recognize plan and policy limits. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do required reviewers protect a production deployment?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[05 - Advanced CI CD/04 - Staging and Production|Staging and Production]].

## Introduction, purpose, and how it works

An environment protection rule can hold a job before it runs or receives environment secrets. It lets reviewers assess the release and target before access is granted. Required reviewers and other protection features depend on repository visibility and GitHub plan; verify the repository’s available settings. Naming an environment in YAML does not create an effective review policy by itself.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `environment.name: production` | Request a protected environment | Use the exact configured name. |
| `Required reviewers` | Hold a job for human review | Configure in Settings; availability and bypass rules matter. |

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
  approval-demo:
    runs-on: ubuntu-24.04
    environment:
      name: production
    steps:
      - run: printf "Approved policy demonstration; no deployment\n"
```

## Step-by-step walkthrough

1. In a practice repository open Settings, Environments, production. Configure required reviewers if supported, restrict deployment branches, and consider preventing self-review.

2. Review administrator bypass policy and who can edit workflows or environment configuration. Approval is weak if untrusted actors can rewrite the job that receives secrets.

3. Add the practice workflow and dispatch it. An authorized reviewer should see a pending deployment review.

4. Before approving a real release inspect commit, tests, digest, target, migration impact, rollback readiness, and monitoring plan. The example itself only prints a message.

## Expected result

The job waits when the configured protection applies, then runs after authorized review.

## Practice

Use separate author and reviewer accounts where available. Success: confirm the author cannot bypass the configured rule.

## Common errors and troubleshooting

Job runs immediately: confirm exact environment spelling, supported features, branch policy, and bypass settings. No eligible reviewer: adjust policy through an authorized repository administrator.

## Knowledge check

1. When are environment secrets released?
2. Does YAML alone add reviewers?
3. Can plan affect features?
4. What should reviewers inspect?
5. Does approval prove correctness?

> [!question]- Answers
> 1. After applicable protection gates pass.
> 2. No.
> 3. Yes.
> 4. Artifact, target, evidence, migration and rollback impact.
> 5. No.

## Cleanup

No deployment occurs. Keep environment settings only if still needed for your practice repository.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[05 - Advanced CI CD/04 - Staging and Production|Staging and Production]]
- [[05 - Advanced CI CD/06 - Container Registry Authentication|Next in the roadmap: Container Registry Authentication]]

## Official references

- [Env official documentation](https://docs.github.com/en/actions/how-tos/deploy/configure-and-manage-deployments/manage-environments)
- [Security official documentation](https://docs.github.com/en/actions/reference/security/secure-use)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[05 - Advanced CI CD/04 - Staging and Production\|Staging and Production]] | [[05 - Advanced CI CD/06 - Container Registry Authentication\|Container Registry Authentication]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
