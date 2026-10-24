---
title: "Automatic Docker Builds"
subject: "GitHub Actions"
level: projects
tags:
  - github-actions
  - projects
---

# Automatic Docker Builds

Build the repository Dockerfile on pull requests.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Build the repository Dockerfile on pull requests. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I build an image automatically without publishing it?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[01 - Docker Fundamentals/03 - Dockerfile Instructions|Dockerfile Instructions]], [[03 - GitHub Actions Fundamentals/06 - Uses vs Run|Uses vs Run]].

## Introduction, purpose, and how it works

A build-only check catches missing files, failed dependency installation, and invalid Dockerfile steps without releasing an artifact. Checkout supplies the repository as a build context. The image stays on the job’s runner unless explicitly pushed or exported. Build success alone is weaker than runtime and integration tests.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `docker build --tag NAME .` | Build the default Dockerfile | Use for source validation; a tag labels the local result. |
| `permissions contents: read` | Read repository contents | Use for checkout without write operations. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** CI demonstration in a practice repository. The walkthrough identifies filenames and the machine where each operation belongs.

```yaml
name: Image build check
on:
  pull_request:
permissions:
  contents: read
jobs:
  build:
    runs-on: ubuntu-24.04
    timeout-minutes: 15
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
        with:
          persist-credentials: false
      - run: docker build --tag lesson-app:ci .
```

## Step-by-step walkthrough

1. Use a practice repository containing the Dockerfile and all context files from Dockerfile Instructions or Multi-stage Docker Builds.

2. Checkout uses a full commit pin. Review upstream updates before adoption; see Uses vs Run for the pinning procedure.

3. The final dot supplies the repository directory as context. A .dockerignore should exclude secrets and unnecessary files before the build is submitted.

4. Open a pull request after adding the practice workflow. The job has only contents: read and no registry login or push step.

## Expected result

The image builds on the runner and no registry artifact is published.

## Practice

Intentionally break a COPY source in a practice branch. Success: the check fails at the build step.

## Common errors and troubleshooting

COPY missing: review the context and .dockerignore. Private dependency access: design secret mounts rather than adding a token through ARG or COPY. Different architecture: compare runner platform and target platform.

## Knowledge check

1. What supplies source?
2. Does build publish?
3. What is the dot?
4. Should .env enter that context?
5. Does success prove runtime behavior?

> [!question]- Answers
> 1. Checkout.
> 2. No.
> 3. The build context.
> 4. Normally no.
> 5. No.

## Cleanup

Hosted runner disposal removes the image. Local replicas can remove lesson-app:ci explicitly after use.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[01 - Docker Fundamentals/03 - Dockerfile Instructions|Dockerfile Instructions]]
- [[03 - GitHub Actions Fundamentals/06 - Uses vs Run|Uses vs Run]]
- [[04 - Docker and GitHub Actions/03 - Running Containers in CI|Next in the roadmap: Running Containers in CI]]

## Official references

- [Ci official documentation](https://docs.docker.com/build/ci/github-actions/)
- [File official documentation](https://docs.docker.com/reference/dockerfile/)
- [Security official documentation](https://docs.github.com/en/actions/reference/security/secure-use)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[04 - Docker and GitHub Actions/01 - Docker in GitHub Actions\|Docker in GitHub Actions]] | [[04 - Docker and GitHub Actions/03 - Running Containers in CI\|Running Containers in CI]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
