---
title: "Dockerfile Instruction Reference"
subject: "Docker"
level: reference
tags:
  - docker
  - reference
---

# Dockerfile Instruction Reference

Look up build-time and runtime instructions without conflating them.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Look up build-time and runtime instructions without conflating them. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- What do the main Dockerfile instructions mean?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[01 - Docker Fundamentals/03 - Dockerfile Instructions|Dockerfile Instructions]].

## Introduction, purpose, and how it works

A Dockerfile combines filesystem construction and startup metadata. Build instructions may produce cached layers; metadata instructions configure later builds or runtime behavior. Order affects cache reuse and permissions. Only the final effective CMD and ENTRYPOINT in a stage determine its startup defaults. Avoid treating every instruction as a process running simultaneously.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `FROM IMAGE AS NAME` | Start and optionally name a stage | Use for stage boundaries. |
| `WORKDIR /path` | Set directory context | Use absolute paths for clarity. |
| `COPY SOURCE DEST` | Copy build-context content | Use .dockerignore to exclude sensitive files. |
| `RUN COMMAND` | Execute build preparation | Use cache-aware ordering; do not expect its processes to persist at runtime. |
| `ARG NAME=VALUE` | Declare a build argument | Use for non-secret build parameters; scope matters. |
| `ENV NAME=value` | Store runtime environment defaults | Use only non-sensitive configuration. |
| `EXPOSE PORT` | Document a listening port | Use as metadata; publish explicitly with -p or ports. |
| `USER UID:GID` | Select process identity | Prepare required writable paths before switching. |
| `ENTRYPOINT ["tool"]` | Set the main executable | Use exec form for direct startup. |
| `CMD ["args"]` | Set replaceable defaults | Use with clear override behavior. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```dockerfile
FROM alpine:3.22
WORKDIR /app
COPY message.txt /app/message.txt
RUN chmod 444 /app/message.txt
ENV APP_MODE=lesson
USER 10001:10001
ENTRYPOINT ["cat"]
CMD ["/app/message.txt"]
```

## Step-by-step walkthrough

1. In a new scratch directory create message.txt with readable text and save the block as Dockerfile.

2. FROM selects the base, WORKDIR sets path context, COPY adds the file, and RUN changes its permissions during the build.

3. ENV sets a non-secret default; USER selects the runtime identity. Exec-form ENTRYPOINT plus CMD means cat reads the default path unless arguments override it.

4. Build with `docker build -t lesson-reference .` and run `docker run --rm lesson-reference`. A non-root process can read the world-readable file without writing it.

## Expected result

The image prints the message file as the configured non-root user.

## Practice

Override the command argument with /etc/os-release. Success: explain why cat remains the executable.

## Common errors and troubleshooting

Permission failure: parent directories need traversal permission as well as file read permission. An ARG declared in the wrong stage may be unavailable. EXPOSE alone does not publish a host port.

## Knowledge check

1. What selects a base?
2. What executes during build?
3. What supplies runtime defaults?
4. Does EXPOSE publish?
5. Should ENV carry secrets?

> [!question]- Answers
> 1. FROM.
> 2. RUN.
> 3. CMD.
> 4. No.
> 5. No.

## Cleanup

The container removes itself; remove only lesson-reference if you no longer need its local image.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[01 - Docker Fundamentals/03 - Dockerfile Instructions|Dockerfile Instructions]]
- [[07 - References/03 - Docker Compose YAML Reference|Next in the roadmap: Docker Compose YAML Reference]]

## Official references

- [File official documentation](https://docs.docker.com/reference/dockerfile/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[07 - References/01 - Docker Command Cheat Sheet\|Docker Command Cheat Sheet]] | [[07 - References/03 - Docker Compose YAML Reference\|Docker Compose YAML Reference]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
