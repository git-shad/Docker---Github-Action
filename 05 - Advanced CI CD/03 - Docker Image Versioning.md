---
title: "Docker Image Versioning"
subject: "GitHub Actions"
level: advanced
tags:
  - github-actions
  - advanced
---

# Docker Image Versioning

Choose artifact identifiers suited to debugging, release discovery, and deployment.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Choose artifact identifiers suited to debugging, release discovery, and deployment. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- Should I deploy latest, a commit tag, a release tag, or a digest?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[05 - Advanced CI CD/02 - GitHub Container Registry|GitHub Container Registry]].

## Introduction, purpose, and how it works

latest is just a tag convention; it does not mean newest, stable, or compatible. Commit tags identify a source revision by convention but can be overwritten. Semantic release tags such as 1.2.3 communicate compatibility policy when a project follows it. A digest fixes image content, including its dependency build result. The same source can rebuild differently when base tags or remote dependencies move.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `docker tag SOURCE TARGET` | Add a local image alias | Use for readable release labels; it does not publish. |
| `IMAGE@sha256:DIGEST` | Identify immutable registry content | Use a real captured digest for deployment. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```bash
read -r -p "Existing local image tag: " SOURCE_IMAGE
read -r -p "Additional local release tag: " RELEASE_TAG
docker tag "$SOURCE_IMAGE" "$RELEASE_TAG"
docker image inspect --format '{{.Id}} {{json .RepoTags}}' "$SOURCE_IMAGE"
```

## Step-by-step walkthrough

1. Run in Ubuntu Bash on a local lab image. Enter an existing source tag such as lesson-react and a new local name such as lesson-react:1.0.0.

2. docker tag adds a local alias, not a second build or an upload. Both tags initially refer to the same image ID.

3. Inspect the image to see aliases. If publishing later, push explicitly to the intended registry and capture its reported digest.

4. For release records store source SHA, image digest, build run, platform, configuration version, and migration compatibility. Retain previous artifacts long enough to support rollback.

## Expected result

Two local tags identify one image object. No registry change occurs.

## Practice

Rebuild source under a new tag and compare IDs. Success: distinguish source version, local image ID, and registry digest.

## Common errors and troubleshooting

Invalid reference: review lowercase repository naming and tag characters. Missing RepoDigests for a locally built image is normal until registry relationships exist; image ID is not a substitute for the registry manifest digest.

## Knowledge check

1. Does latest guarantee newest?
2. Does docker tag rebuild?
3. Can commit tags move?
4. What freezes deployed content?
5. Can same source rebuild differently?

> [!question]- Answers
> 1. No.
> 2. No.
> 3. Yes.
> 4. A digest reference.
> 5. Yes, if build inputs move.

## Cleanup

If desired remove only the added local alias with docker image rm and its exact tag. Avoid deleting rollback artifacts before their retention window ends.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[05 - Advanced CI CD/02 - GitHub Container Registry|GitHub Container Registry]]
- [[05 - Advanced CI CD/04 - Staging and Production|Next in the roadmap: Staging and Production]]

## Official references

- [Registry official documentation](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)
- [File official documentation](https://docs.docker.com/reference/dockerfile/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[05 - Advanced CI CD/02 - GitHub Container Registry\|GitHub Container Registry]] | [[05 - Advanced CI CD/04 - Staging and Production\|Staging and Production]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
