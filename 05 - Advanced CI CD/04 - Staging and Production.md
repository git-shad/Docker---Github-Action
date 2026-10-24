---
title: "Staging and Production"
subject: "GitHub Actions"
level: advanced
tags:
  - github-actions
  - advanced
---

# Staging and Production

Separate environment configuration from immutable release artifacts.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Separate environment configuration from immutable release artifacts. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I promote the same image through staging and production?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[04 - Docker and GitHub Actions/10 - Preventing Failed Deployments|Preventing Failed Deployments]].

## Introduction, purpose, and how it works

Staging is a non-production environment used to test a release under representative configuration. Production serves real users and real data. Build once and promote the same digest so approval refers to the artifact actually tested. Configuration, secrets, data, DNS, and capacity still differ by environment. Never copy production personal data into an unprotected learning stack.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `environment: staging` | Select staging policy and scoped settings | Use a deliberately configured environment. |
| `needs: staging` | Wait for staging job success | Use with actual runtime checks in a real release. |

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
  staging:
    runs-on: ubuntu-24.04
    environment: staging
    steps:
      - run: printf "Staging policy gate only; no deployment\n"
  production:
    needs: staging
    runs-on: ubuntu-24.04
    environment: production
    steps:
      - run: printf "Production policy gate only; no deployment\n"
```

## Step-by-step walkthrough

1. Create separate staging and production environments in a practice repository with distinct configuration and access policy. This workflow demonstrates sequencing, not actual promotion or deployment.

2. The staging job requests staging-scoped settings. The production job waits for it and then requests its own environment.

3. Configure reviewers and allowed deployment branches where supported. Use different database credentials and target accounts; production access should not be needed for staging tests.

4. Design a real release record that carries the same validated image digest across both jobs. Files or values must be transferred explicitly; needs does not share a runner filesystem. Frontend build-time URLs complicate build-once promotion: prefer runtime-loaded public configuration if the architecture supports it.

## Expected result

The practice run demonstrates separate environment gates. No server is changed.

## Practice

Write a configuration inventory showing what stays in the image and what differs by environment. Success: no production secret is required to build the artifact.

## Common errors and troubleshooting

Staging succeeds but production fails: compare config, data shape, networking, architecture, and capacity. Rebuilding separately for production invalidates evidence that staging tested the exact same bytes.

## Knowledge check

1. What should be promoted unchanged?
2. What differs by environment?
3. Does this example deploy?
4. Why avoid production data in labs?
5. Does needs share files?

> [!question]- Answers
> 1. The tested digest.
> 2. Configuration, credentials, data, and infrastructure.
> 3. No.
> 4. Privacy and access boundaries.
> 5. No.

## Cleanup

No resources need deletion. Remove only practice environment settings when no longer referenced.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[04 - Docker and GitHub Actions/10 - Preventing Failed Deployments|Preventing Failed Deployments]]
- [[05 - Advanced CI CD/05 - Deployment Approvals|Next in the roadmap: Deployment Approvals]]

## Official references

- [Env official documentation](https://docs.github.com/en/actions/how-tos/deploy/configure-and-manage-deployments/manage-environments)
- [Jobs official documentation](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-jobs)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[05 - Advanced CI CD/03 - Docker Image Versioning\|Docker Image Versioning]] | [[05 - Advanced CI CD/05 - Deployment Approvals\|Deployment Approvals]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
