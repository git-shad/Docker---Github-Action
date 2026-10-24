---
title: "Docker Compose"
subject: "Docker"
level: fundamentals
tags:
  - docker
  - fundamentals
---

# Docker Compose

Start a repeatable local web service from a declarative file.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Start a repeatable local web service from a declarative file. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How does Compose describe and start multiple services?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[01 - Docker Fundamentals/03 - Dockerfile Instructions|Dockerfile Instructions]].

## Introduction, purpose, and how it works

Compose maps a YAML service definition to containers, networks, and volumes. A service is a logical component such as `web` or `db`; its container is a concrete running instance. YAML uses indentation to express nesting. Modern Compose uses the Compose Specification and needs no top-level `version` field.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `services:` | Declare components | Use at the top level; service properties belong below a service key. |
| `ports: ["HOST_IP:HOST:CONTAINER"]` | Publish a port | Use for host access; this is unnecessary for service-to-service communication. |
| `docker compose up -d` | Create or reconcile services | Use after validation; editing a file alone does not apply it. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```yaml
services:
  web:
    image: nginx:stable-alpine
    ports:
      - "127.0.0.1:8080:80"
```

## Step-by-step walkthrough

1. In a new local lab directory, save this as `compose.yaml`. Do not mix unrelated projects in that directory.

2. `services` contains the application components. `web` is a service key; `image` selects a downloadable image.

3. The port mapping binds host loopback port 8080 to container port 80. It exposes HTTP to your machine only; omitting the loopback address can expose it on other host interfaces.

4. Run `docker compose config --quiet` to validate the model, then `docker compose up -d` to start it in the background.

5. Open `http://127.0.0.1:8080`, then inspect `docker compose ps` and `docker compose logs web`.

## Expected result

The nginx welcome page loads and the web service is running. No database is implied by this one-service file.

## Practice

Change only the host port to 8081 and recreate with `docker compose up -d`. Success: the new URL responds.

## Common errors and troubleshooting

“Port already allocated”: choose another host port. “No configuration file”: change into the directory containing `compose.yaml` or supply `-f`.

## Knowledge check

1. Is Compose an image builder only?
2. What is web?
3. Which port is nginx listening on inside?
4. Why use 127.0.0.1?
5. Does version: 3 need adding?

> [!question]- Answers
> 1. No, it coordinates service resources.
> 2. A service key.
> 3. 80.
> 4. Limit the host binding to loopback.
> 5. No.

## Cleanup

`docker compose down` stops and removes this project’s containers and default network. It does not remove the downloaded image.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[01 - Docker Fundamentals/03 - Dockerfile Instructions|Dockerfile Instructions]]
- [[01 - Docker Fundamentals/05 - Docker Volumes|Next in the roadmap: Docker Volumes]]

## Official references

- [Compose official documentation](https://docs.docker.com/reference/compose-file/services/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[01 - Docker Fundamentals/03 - Dockerfile Instructions\|Dockerfile Instructions]] | [[01 - Docker Fundamentals/05 - Docker Volumes\|Docker Volumes]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
