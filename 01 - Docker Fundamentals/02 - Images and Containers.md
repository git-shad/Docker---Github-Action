---
title: Images and Containers
subject: Docker
level: fundamentals
tags:
  - docker
  - fundamentals
aliases:
---

# Images and Containers

Create two containers from one image and inspect their separate identities.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Create two containers from one image and inspect their separate identities. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- What is the difference between an image and a container?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[01 - Docker Fundamentals/01 - Introduction to Docker|Introduction to Docker]].

## Introduction, purpose, and how it works

An image is a layered, read-only filesystem template plus startup configuration. A container adds a writable layer and process state. Many containers can use one image without sharing their writable layers. A registry stores distributable images; your Engine caches images locally. A tag such as `alpine:3.22` is a movable name, not a cryptographic identity.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `docker create --name NAME IMAGE COMMAND` | Prepare a container | Use when creation and startup must be separate; duplicate names fail. |
| `docker start -a NAME` | Start and attach | Use with an existing container; it does not accept a new image. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```bash
docker pull alpine:3.22
docker create --name lesson-one alpine:3.22 echo first
docker create --name lesson-two alpine:3.22 echo second
docker start -a lesson-one
docker start -a lesson-two
docker ps -a --filter name=lesson-
```

## Step-by-step walkthrough

1. Use Ubuntu Bash with Engine access. Pull downloads the chosen image without starting a container.

2. Each `create` allocates a different named container and stores its command. Neither command runs yet.

3. Each `start -a` starts the saved command and attaches to its output. The processes print different words then exit.

4. List all matching containers, including stopped ones. Compare container IDs with the shared image name.

## Expected result

Two stopped containers reference Alpine and have different IDs. Example output words are `first` and `second`.

## Practice

Create a third named container using the same image. Success: three container IDs, still one image reference.

## Common errors and troubleshooting

“Name already in use”: remove the old lesson container or choose a new name. A stopped container is not a missing image; inspect `docker ps -a`.

## Knowledge check

1. Can one image start many containers?
2. Where do container writes go by default?
3. Does exit remove a normal container?
4. What does a registry store?
5. Is a tag immutable?

> [!question]- Answers
> 1. Yes.
> 2. Its writable layer.
> 3. No, unless automatic removal was requested.
> 4. Image manifests and layers.
> 5. No; a digest identifies content.

## Cleanup

After inspection, `docker rm lesson-one lesson-two` deletes those stopped container layers. Remove your third container by its chosen name too; image removal is separate.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[01 - Docker Fundamentals/01 - Introduction to Docker|Introduction to Docker]]
- [[01 - Docker Fundamentals/03 - Dockerfile Instructions|Next in the roadmap: Dockerfile Instructions]]

## Official references

- [Docker official documentation](https://docs.docker.com/get-started/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[01 - Docker Fundamentals/01 - Introduction to Docker\|Introduction to Docker]] | [[01 - Docker Fundamentals/03 - Dockerfile Instructions\|Dockerfile Instructions]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
