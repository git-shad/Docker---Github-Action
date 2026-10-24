---
title: "Docker Networks"
subject: "Docker"
level: fundamentals
tags:
  - docker
  - fundamentals
---

# Docker Networks

Use network DNS to reach another container.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Use network DNS to reach another container. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do containers find each other without publishing ports?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[01 - Docker Fundamentals/05 - Docker Volumes|Docker Volumes]].

## Introduction, purpose, and how it works

A user-defined bridge network connects containers on one Engine. Docker DNS resolves container names on that network. `localhost` inside a container refers to that container, not the host or its neighbors. Published ports are for access through the host; peers use their service or container name and internal port.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `--network NAME` | Attach a container to a network | Use for peer connectivity; both peers must share a network. |
| `docker network inspect NAME` | Inspect network membership | Use to diagnose attachment; IPs are not stable application configuration. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```bash
docker network create lesson-net
docker run -d --name lesson-web --network lesson-net nginx:stable-alpine
docker run --rm --network lesson-net alpine:3.22 wget -qO- http://lesson-web:80
```

## Step-by-step walkthrough

1. Run from Ubuntu Bash. Network creation establishes an isolated logical network on the local Engine.

2. Start nginx attached to it. There is intentionally no `-p`: no host port is needed for the peer request.

3. The temporary Alpine client resolves `lesson-web` through Docker DNS and sends HTTP to port 80.

4. Compare `docker network inspect lesson-net` with `docker inspect lesson-web` to see network attachment metadata.

## Expected result

The client prints nginx HTML. Host browser access is not established by these commands.

## Practice

Run a client without `--network lesson-net`. Success: explain its failure to resolve the network-scoped name.

## Common errors and troubleshooting

“Bad address”: check network membership and name. “Connection refused”: DNS may work while the target process is not ready or uses a different port.

## Knowledge check

1. What resolves the name?
2. Does localhost reach another container?
3. Must every internal port be published?
4. What is a bridge network scoped to?
5. Does DNS success imply readiness?

> [!question]- Answers
> 1. Docker DNS on the user-defined network.
> 2. No.
> 3. No.
> 4. A Docker host.
> 5. No.

## Cleanup

`docker stop lesson-web` then `docker rm lesson-web` removes the lab server; `docker network rm lesson-net` removes the now-unused lab network.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[01 - Docker Fundamentals/05 - Docker Volumes|Docker Volumes]]
- [[01 - Docker Fundamentals/07 - Docker Commands|Next in the roadmap: Docker Commands]]

## Official references

- [Network official documentation](https://docs.docker.com/engine/network/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[01 - Docker Fundamentals/05 - Docker Volumes\|Docker Volumes]] | [[01 - Docker Fundamentals/07 - Docker Commands\|Docker Commands]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
