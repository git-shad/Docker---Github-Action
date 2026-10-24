---
title: "Docker Commands"
subject: "Docker"
level: fundamentals
tags:
  - docker
  - fundamentals
---

# Docker Commands

Follow a container from creation through logs, inspection, stopping, and removal.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Follow a container from creation through logs, inspection, stopping, and removal. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- Which Docker commands do I use to inspect and clean up a container?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[01 - Docker Fundamentals/06 - Docker Networks|Docker Networks]].

## Introduction, purpose, and how it works

Docker distinguishes image operations from container lifecycle operations. `run` creates a new container, while `start` reuses an existing one. `exec` runs an additional process only inside a running container. Inspect metadata and logs before removing a failed container, because removal loses its writable layer and local diagnostic history.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `docker logs --tail N NAME` | Read captured process output | Use for bounded diagnostics; programs must write to stdout or stderr. |
| `docker exec NAME COMMAND` | Run a process in a live container | Use for inspection; installing fixes interactively does not update the image recipe. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```bash
docker run -d --name lesson-clock alpine:3.22 sh -c 'while true; do date; sleep 5; done'
docker ps --filter name=lesson-clock
docker logs --tail 3 lesson-clock
docker exec lesson-clock cat /etc/os-release
docker inspect --format '{{.State.Status}}' lesson-clock
docker stop lesson-clock
```

## Step-by-step walkthrough

1. Run in Ubuntu Bash. `-d` detaches from a named long-running process.

2. `ps` reports running containers. `logs --tail 3` limits output to the latest lines; do not publish logs containing credentials.

3. `exec` starts `cat` in the live container. The inspection format extracts a specific metadata field.

4. `stop` asks the main process to terminate, then may force termination after its timeout. Use `docker ps -a` to see the stopped container.

## Expected result

Timestamps appear in logs and the final container state is exited.

## Practice

Start the stopped container using `docker start lesson-clock`. Success: logs continue without creating a new container ID.

## Common errors and troubleshooting

“Container is not running”: `exec` cannot run after stop. “No such container”: check name or ID and Docker context. Avoid using `rm -f` as the first debugging step.

## Knowledge check

1. Does run reuse a named stopped container?
2. What shows stopped containers?
3. What does exec require?
4. What should precede removal?
5. Does stop delete the container?

> [!question]- Answers
> 1. No.
> 2. docker ps -a.
> 3. A running container.
> 4. Capture needed logs and inspect state.
> 5. No.

## Cleanup

Stop it again if restarted, then `docker rm lesson-clock`. This deletes only that stopped container, not its base image.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[01 - Docker Fundamentals/06 - Docker Networks|Docker Networks]]
- [[Docker/02 - Fundamentals/Docker CLI Flags and Combinations|Next in the roadmap: Docker CLI Flags and Combinations]]

## Official references

- [Docker official documentation](https://docs.docker.com/get-started/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[01 - Docker Fundamentals/06 - Docker Networks\|Docker Networks]] | [[Docker/02 - Fundamentals/Docker CLI Flags and Combinations\|Docker CLI Flags and Combinations]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
