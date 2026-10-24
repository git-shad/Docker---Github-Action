---
title: "Publishing Docker Images"
subject: "GitHub Actions"
level: projects
tags:
  - github-actions
  - projects
---

# Publishing Docker Images

Build, smoke-test, authenticate, and push a commit-tagged image.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Build, smoke-test, authenticate, and push a commit-tagged image. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I publish a tested image to GitHub Container Registry?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[01 - Docker Fundamentals/03 - Dockerfile Instructions|Dockerfile Instructions]], [[04 - Docker and GitHub Actions/03 - Running Containers in CI|Running Containers in CI]], [[03 - GitHub Actions Fundamentals/07 - Secrets and Variables|Secrets and Variables]].

## Introduction, purpose, and how it works

Publishing uploads an image so other machines can pull it. This changes an external registry and may affect storage or usage costs. A commit-based tag connects the artifact to source; it is still a mutable tag, so record the returned digest for deployment identity. This focused exercise publishes the tiny lesson-cat image, not a production Laravel release.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `packages: write` | Allow package uploads for the job | Grant only to trusted publishing jobs. |
| `docker login --password-stdin` | Authenticate without a password CLI argument | Use the built-in token inside Actions. |
| `docker push IMAGE:TAG` | Upload image artifacts | Use only after tests; it changes an external registry. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** CI demonstration in a practice repository. The walkthrough identifies filenames and the machine where each operation belongs.

```yaml
name: Publish tested image
on:
  push:
    branches: [main]
permissions:
  contents: read
jobs:
  publish:
    runs-on: ubuntu-24.04
    timeout-minutes: 15
    permissions:
      contents: read
      packages: write
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
        with:
          persist-credentials: false
      - name: Build and smoke test
        run: |
          image="ghcr.io/${GITHUB_REPOSITORY,,}"
          docker build -t "$image:$GITHUB_SHA" .
          actual="$(docker run --rm "$image:$GITHUB_SHA")"
          test "$actual" = "built into image"
      - name: Authenticate and publish
        env:
          GHCR_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          image="ghcr.io/${GITHUB_REPOSITORY,,}"
          printf '%s' "$GHCR_TOKEN" | docker login ghcr.io -u "$GITHUB_ACTOR" --password-stdin
          docker push "$image:$GITHUB_SHA"
      - name: Remove local registry login
        if: always()
        run: docker logout ghcr.io
```

## Step-by-step walkthrough

1. Use a practice repository containing the lesson-cat Dockerfile and greeting.txt from Dockerfile Instructions. Only run this workflow if you intend to create a registry package. It triggers after a push to main; branch protection and review should guard that branch.

2. The built-in token gets packages: write only in the publishing job, plus contents: read for checkout. Registry/package policy must allow this repository to publish the target name.

3. Bash lowercases GITHUB_REPOSITORY because image paths must be lowercase. Build once using the full commit SHA as tag, then assert the known output before any push.

4. Use --password-stdin to avoid placing the token in CLI arguments. Do not enable shell tracing. docker push uploads the tested image and reports its digest.

5. Inspect package visibility explicitly: do not assume a newly created package is public. Record source SHA and pushed digest, then remove local login state.

## Expected result

The GHCR package receives a commit-tagged image only if the build and smoke test pass.

## Practice

In a practice repo, break the smoke test before publishing. Success: authentication/push never execute for the failing candidate.

## Common errors and troubleshooting

Denied: check job permissions, existing package linkage, organization policy, and package Actions access. Invalid reference: confirm lowercase namespace. A digest must be captured from the actual push, not invented.

## Knowledge check

1. What permission adds upload access?
2. Why lowercase?
3. Is a commit tag immutable?
4. What uniquely identifies image content?
5. Does a failed smoke test publish?

> [!question]- Answers
> 1. packages: write.
> 2. Registry path requirements.
> 3. No.
> 4. Its digest.
> 5. No.

## Cleanup

Logout is included. Deleting a published package is a separate destructive registry operation; inspect dependents and retention policy before doing so.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[01 - Docker Fundamentals/03 - Dockerfile Instructions|Dockerfile Instructions]]
- [[04 - Docker and GitHub Actions/03 - Running Containers in CI|Running Containers in CI]]
- [[03 - GitHub Actions Fundamentals/07 - Secrets and Variables|Secrets and Variables]]
- [[04 - Docker and GitHub Actions/10 - Preventing Failed Deployments|Next in the roadmap: Preventing Failed Deployments]]

## Official references

- [Publish official documentation](https://docs.github.com/en/actions/tutorials/publish-packages/publish-docker-images)
- [Registry official documentation](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)
- [Security official documentation](https://docs.github.com/en/actions/reference/security/secure-use)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[04 - Docker and GitHub Actions/08 - Container Cleanup\|Container Cleanup]] | [[04 - Docker and GitHub Actions/10 - Preventing Failed Deployments\|Preventing Failed Deployments]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
