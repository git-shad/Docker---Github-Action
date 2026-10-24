---
title: "Docker Volumes"
subject: "Docker"
level: fundamentals
tags:
  - docker
  - fundamentals
---

# Docker Volumes

Demonstrate persistent storage independently of a database.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Demonstrate persistent storage independently of a database. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How does a volume preserve data after a container is removed?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[01 - Docker Fundamentals/04 - Docker Compose|Docker Compose]].

## Introduction, purpose, and how it works

A named volume is storage managed by Docker outside a container’s writable layer. Containers can be replaced while the volume remains. A bind mount instead points at a specific host path. Prefer named volumes for local database data and bind mounts for source editing. Persistence does not provide backups or protect against accidental SQL deletion.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `--mount type=volume,src=NAME,dst=PATH` | Attach managed storage | Use for persistent application data; wrong destination leaves writes elsewhere. |
| `readonly` | Prevent writes through the mount | Use for readers; do not use it for a database data directory. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```bash
docker volume create lesson-data
docker run --rm --mount type=volume,src=lesson-data,dst=/data alpine:3.22 sh -c 'printf "persistent\n" > /data/message.txt'
docker run --rm --mount type=volume,src=lesson-data,dst=/data,readonly alpine:3.22 cat /data/message.txt
```

## Step-by-step walkthrough

1. Run in Ubuntu Bash. Create a uniquely named lab volume.

2. The first container writes into the mounted directory, so its text goes to the volume rather than its disposable writable layer.

3. `--rm` removes that container. The second container mounts the same volume read-only and reads the surviving file.

4. Use `docker volume inspect lesson-data` to view metadata. Treat Engine-managed paths as implementation details; do not manually edit a live database there.

## Expected result

The second container prints `persistent` even though the writer no longer exists.

## Practice

Write another file in a fresh writer and read it in a fresh reader. Success: data outlives both container instances.

## Common errors and troubleshooting

“Read-only file system”: a writer used the read-only mount. Missing files: check volume spelling and mount destination. Mounting over an existing directory hides underlying container files.

## Knowledge check

1. Where is durable data?
2. Does --rm remove this named volume?
3. Can readonly mounts write?
4. Is a volume a backup?
5. What does volume rm destroy?

> [!question]- Answers
> 1. In the named volume.
> 2. No.
> 3. No.
> 4. No.
> 5. The volume and its contents.

## Cleanup

Only after you no longer need this lab data, `docker volume rm lesson-data` irreversibly deletes it. Do not use broad volume pruning on a machine with important data.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[01 - Docker Fundamentals/04 - Docker Compose|Docker Compose]]
- [[01 - Docker Fundamentals/06 - Docker Networks|Next in the roadmap: Docker Networks]]

## Official references

- [Volume official documentation](https://docs.docker.com/engine/storage/volumes/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[01 - Docker Fundamentals/04 - Docker Compose\|Docker Compose]] | [[01 - Docker Fundamentals/06 - Docker Networks\|Docker Networks]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
