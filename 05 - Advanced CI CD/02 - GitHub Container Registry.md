---
title: "GitHub Container Registry"
subject: "GitHub Actions"
level: advanced
tags:
  - github-actions
  - advanced
---

# GitHub Container Registry

Inspect a registry artifact without confusing source access with package access.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Inspect a registry artifact without confusing source access with package access. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How are GHCR packages, repositories, tags, and digests related?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[04 - Docker and GitHub Actions/09 - Publishing Docker Images|Publishing Docker Images]].

## Introduction, purpose, and how it works

GHCR stores container images under ghcr.io/namespace/package. A source repository and an image package are different resources. Linking a package to a repository can simplify permissions and provenance, but an existing package may need explicit Actions access. Public images can be pulled without authentication; private ones require access. A tag is a name resolving to a manifest, while a digest is content-addressed identity.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `ghcr.io/NAMESPACE/PACKAGE:TAG` | Name a registry artifact | Use a lowercase image path. |
| `docker buildx imagetools inspect REF` | Read registry manifest metadata | Use to identify digest and platform support. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```bash
read -r -p "Image reference to inspect: " IMAGE_REF
docker buildx imagetools inspect "$IMAGE_REF"
```

## Step-by-step walkthrough

1. Use Ubuntu Bash with Docker Buildx available and a real image reference from Publishing Docker Images, or an authorized public image. The command queries registry metadata and does not deploy.

2. Enter the full image name at the prompt. For a private package, authenticate first using the registry authentication lesson.

3. Inspect the manifest digest and platform list. Multi-platform images can have an index digest plus different per-platform manifest digests.

4. Open the package page in GitHub and inspect visibility, linked repository, and access settings rather than assuming repository permissions are identical.

## Expected result

The inspection reports the real manifest metadata and supported architectures.

## Practice

Inspect a tag and then its returned digest reference. Success: explain which is a movable name and which is a fixed content identity.

## Common errors and troubleshooting

Unauthorized: package permissions or login may differ from repository access. Manifest unknown: tag or namespace may be wrong. Unsupported platform: publish or choose an artifact matching the server architecture.

## Knowledge check

1. Is a repository the package itself?
2. Can public packages be pulled anonymously?
3. What can move?
4. Why multiple platform digests?
5. What must existing packages grant?

> [!question]- Answers
> 1. No.
> 2. Yes.
> 3. A tag.
> 4. Each platform can have a different manifest.
> 5. Appropriate workflow/repository access.

## Cleanup

Inspection creates no server resources. Log out if you used a temporary local registry login.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[04 - Docker and GitHub Actions/09 - Publishing Docker Images|Publishing Docker Images]]
- [[05 - Advanced CI CD/03 - Docker Image Versioning|Next in the roadmap: Docker Image Versioning]]

## Official references

- [Registry official documentation](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)
- [Publish official documentation](https://docs.github.com/en/actions/tutorials/publish-packages/publish-docker-images)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[05 - Advanced CI CD/01 - Deployment to Ubuntu Server\|Deployment to Ubuntu Server]] | [[05 - Advanced CI CD/03 - Docker Image Versioning\|Docker Image Versioning]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
