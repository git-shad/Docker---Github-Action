---
title: "Docker Compose Examples"
subject: "GitHub Actions"
level: projects
tags:
  - github-actions
  - projects
---

# Docker Compose Examples

Match each service boundary to its actual runtime responsibilities.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Match each service boundary to its actual runtime responsibilities. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I choose between a frontend-only and a Laravel database Compose stack?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[02 - Docker Development/07 - Multi-stage Docker Builds|Multi-stage Docker Builds]], [[02 - Docker Development/01 - Laravel Docker Environment|Laravel Docker Environment]].

## Introduction, purpose, and how it works

A Compose file should describe one coherent project. A frontend-only stack can be one HTTP service. A Laravel stack needs an application runtime and commonly database, worker, scheduler, and a reverse proxy in production. Development can use Artisan’s server as a learning shortcut; that does not make the same Compose file appropriate for production. Use separate project directories or explicit -f selection rather than accidentally merging unrelated definitions.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `read_only: true` | Restrict root filesystem writes | Use with explicit writable paths required by the process. |
| `tmpfs:` | Provide temporary writable memory-backed mounts | Use for disposable runtime files, not durable data. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```yaml
services:
  frontend:
    image: lesson-react
    ports:
      - "127.0.0.1:8080:80"
    read_only: true
    tmpfs:
      - /var/cache/nginx
      - /var/run
      - /tmp
```

## Step-by-step walkthrough

1. Build lesson-react from Multi-stage Docker Builds first. Save this local frontend-only example as compose.yaml in a separate scratch directory.

2. The image supplies compiled files and nginx. read_only prevents writes to the container root filesystem; tmpfs provides ephemeral writable paths for nginx cache and PID files. Default entrypoint scripts may report read-only template-edit warnings; inspect actual behavior rather than assuming every nginx customization works.

3. Validate and start with `docker compose config --quiet` then `docker compose up -d`. Visit the loopback URL and inspect logs.

4. Compare this with the full two-service file in Laravel Docker Environment. That file uses db-data for persistent database state; tmpfs here is intentionally nonpersistent and must not store MariaDB data.

## Expected result

The static root page responds while ordinary root-filesystem writes are restricted.

## Practice

Inspect service mounts using docker inspect. Success: distinguish temporary RAM-backed paths from persistent volumes.

## Common errors and troubleshooting

Read-only error: identify the exact path a process needs and allow only that path rather than disabling restrictions globally. Image missing: build it on the same Engine first or use an authorized registry image.

## Knowledge check

1. What does read_only protect?
2. Are tmpfs writes persistent?
3. Should database data use this tmpfs?
4. Why a separate directory?
5. Where is the Laravel stack?

> [!question]- Answers
> 1. The container root filesystem.
> 2. No.
> 3. No.
> 4. Avoid unintended configuration/project overlap.
> 5. The linked Laravel Docker Environment lesson.

## Cleanup

docker compose down removes the local service and its ephemeral tmpfs contents. The built image remains.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[02 - Docker Development/07 - Multi-stage Docker Builds|Multi-stage Docker Builds]]
- [[02 - Docker Development/01 - Laravel Docker Environment|Laravel Docker Environment]]
- [[06 - Practical Examples/03 - GitHub Actions YAML Examples|Next in the roadmap: GitHub Actions YAML Examples]]

## Official references

- [Compose official documentation](https://docs.docker.com/reference/compose-file/services/)
- [Volume official documentation](https://docs.docker.com/engine/storage/volumes/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[06 - Practical Examples/01 - Dockerfile Examples\|Dockerfile Examples]] | [[06 - Practical Examples/03 - GitHub Actions YAML Examples\|GitHub Actions YAML Examples]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
