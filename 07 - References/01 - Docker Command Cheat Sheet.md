---
title: "Docker Command Cheat Sheet"
subject: "Docker"
level: reference
tags:
  - docker
  - reference
---

# Docker Command Cheat Sheet

Find inspection, build, run, and cleanup commands by intent.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Find inspection, build, run, and cleanup commands by intent. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- Which Docker commands should I reach for during daily development?
- Where can I look up flag combinations and Docker daemon configuration?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[01 - Docker Fundamentals/07 - Docker Commands|Docker Commands]].

## Introduction, purpose, and how it works

This reference summarizes commands taught in full earlier lessons. Image commands manage templates; container commands manage instances; Compose commands manage a project. Add explicit names or project scope when changing resources. Local Bash examples below inspect only; starting, publishing, and deleting require deliberate selection of a target.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `docker build -t NAME .` | Build a local image | Use with the intended context; it may execute dependency scripts. |
| `docker run --rm IMAGE` | Start a one-off container | Use for disposable tasks; review network and mount access. |
| `docker logs --tail 50 NAME` | Read recent process logs | Use for bounded diagnosis; logs can contain secrets. |
| `docker stop NAME` | Stop a running process | Use with a selected target; it can interrupt service. |
| `docker rm NAME` | Delete a stopped container layer | Use after preserving needed files and evidence. |
| `docker volume rm NAME` | Permanently delete a volume | Use only for confirmed disposable data. |
| `docker compose down` | Stop and remove a project | Use the correct project; --volumes additionally destroys data. |
| `docker push IMAGE:TAG` | Publish an image | Use only with intended registry credentials and release authorization. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Flag combinations by intent

These are option patterns, not standalone commands. Full commands, meaning, prerequisites, verification, and cleanup are in [[Docker/02 - Fundamentals/Docker CLI Flags and Combinations|Docker CLI Flags and Combinations]]. That lesson's official references were checked on 2026-10-24; the examples were not executed.

| Command and options | Combined meaning | Use |
| --- | --- | --- |
| `run --rm -it` | Input, terminal, and automatic removal after exit | Disposable interactive work. |
| `run --rm -i` | Input without a terminal, then automatic removal | Pipes and redirected input. |
| `run -dit` | Detached execution with input and a terminal | A shell for later attachment; ordinary services often need only `-d`. |
| `run -d --rm` | Background execution with removal after exit | Temporary background tasks; preserve needed logs before exit. |
| `run -d -p 127.0.0.1:8080:80` | Background service published on host localhost | Local web lab on Engine 28 or later with bridge networking. |
| `run -d --restart unless-stopped` | Background service with a restart policy | Retained service container. Do not combine a restart policy with `--rm`. |
| `logs -f --tail 50` | Follow output starting with recent lines | Bounded initial log history, then live updates. |
| `ps -aq` | Include stopped containers and print only IDs | Inventory; inspect targets before any deletion. |

Read reused flags in their command's context: `run -p` publishes a port, while `compose -p` selects a project; `logs -f` follows output, while `build -f` selects a Dockerfile and `compose -f` selects a Compose file.

For Engine service settings, use [[Docker/04 - Advanced/Docker Daemon Flags and Configuration|Docker Daemon Flags and Configuration]]. `dockerd --validate --config-file` checks a selected file without starting a daemon. A setting duplicated between daemon startup flags and JSON prevents startup; service-unit flags count too. File validation is distinct from applying and testing a service configuration.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```bash
docker version
docker image ls
docker ps -a
docker volume ls
docker network ls
docker compose ps -a
docker compose logs --tail 50
```

## Step-by-step walkthrough

1. Run in the intended local project directory using Ubuntu Bash and Docker Engine access.

2. Read image ls as cached image references, not running processes. Read ps -a as container instances, including exited ones.

3. Use volume ls and network ls to inventory resources before considering cleanup. List commands do not prove that an apparently unused resource has no valuable data.

4. Use Compose commands for project-scoped status and logs. The table below gives mutation commands separately; inspect the target before invoking them.

## Expected result

You can map a resource name to the right command family without deleting or publishing anything.

## Practice

Find one stopped container and its image. Success: explain why deleting the container and deleting the image are different operations.

## Common errors and troubleshooting

Wrong daemon: inspect docker context show. Empty docker ps: use -a for stopped containers. Missing Compose model: use the correct directory or -f argument. Truncated log evidence: increase tail only as needed.

## Knowledge check

1. What lists templates?
2. What lists instances?
3. What scopes project operations?
4. Do unused volumes lack value?
5. Does inspect publish?

> [!question]- Answers
> 1. docker image ls.
> 2. docker ps -a.
> 3. Compose project identity.
> 4. Not necessarily.
> 5. No.

## Cleanup

The runnable example is inspection-only. For cleanup follow the individual lesson’s exact resource names; do not use global pruning as a shortcut.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[01 - Docker Fundamentals/07 - Docker Commands|Docker Commands]]
- [[Docker/02 - Fundamentals/Docker CLI Flags and Combinations|Docker CLI Flags and Combinations]]
- [[Docker/04 - Advanced/Docker Daemon Flags and Configuration|Docker Daemon Flags and Configuration]]
- [[07 - References/02 - Dockerfile Instruction Reference|Next in the roadmap: Dockerfile Instruction Reference]]

## Official references

- [Docker official documentation](https://docs.docker.com/get-started/)
- [Compose official documentation](https://docs.docker.com/reference/compose-file/services/)
- [Volume official documentation](https://docs.docker.com/engine/storage/volumes/)
- [Container run flags and combinations](https://docs.docker.com/reference/cli/docker/container/run/)
- [Daemon flags and configuration validation](https://docs.docker.com/reference/cli/dockerd/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[06 - Practical Examples/06 - Common Errors and Solutions\|Common Errors and Solutions]] | [[07 - References/02 - Dockerfile Instruction Reference\|Dockerfile Instruction Reference]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
