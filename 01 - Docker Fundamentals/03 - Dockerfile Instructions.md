---
title: "Dockerfile Instructions"
subject: "Docker"
level: fundamentals
tags:
  - docker
  - fundamentals
---

# Dockerfile Instructions

Build a tiny image and predict its startup behavior.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Build a tiny image and predict its startup behavior. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do RUN, CMD, ENTRYPOINT, and COPY differ?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[01 - Docker Fundamentals/02 - Images and Containers|Images and Containers]].

## Introduction, purpose, and how it works

A Dockerfile is a build recipe; Compose is a runtime service description. `RUN` changes the image during a build. `CMD` supplies default startup arguments or a default command. `ENTRYPOINT` fixes the executable to which runtime arguments are appended. `COPY` snapshots files from the build context; it does not keep watching the host directory.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `FROM IMAGE` | Choose the base image | Use first in each build stage; avoid unreviewed floating tags for releases. |
| `RUN COMMAND` | Execute a build step | Use for preparing files; it does not start a persistent runtime service. |
| `ENTRYPOINT ["executable"]` | Set the startup executable | Use for a fixed tool; shell form changes signal handling. |
| `CMD ["argument"]` | Set replaceable defaults | Use for defaults; only the last CMD is effective. |
| `COPY SOURCE DESTINATION` | Snapshot context files | Use for image contents; host absolute paths outside context are unavailable. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```dockerfile
FROM alpine:3.22
WORKDIR /lesson
RUN printf "built into image\n" > message.txt
COPY greeting.txt /lesson/greeting.txt
ENTRYPOINT ["cat"]
CMD ["/lesson/message.txt"]
```

## Step-by-step walkthrough

1. In a new local lab directory, create `greeting.txt` containing a short greeting, and a file named `Dockerfile` containing this block.

2. `FROM` selects the base filesystem. `WORKDIR` selects the directory for later relative paths. `RUN` executes once when the image layer is built.

3. `COPY` includes the local greeting. Only files within the build context are available; exclude `.env`, `.git`, dependencies, and credentials with `.dockerignore`.

4. From that directory run `docker build -t lesson-cat .`. The dot is the build context.

5. Run `docker run --rm lesson-cat`, then `docker run --rm lesson-cat /lesson/greeting.txt`. The second invocation replaces CMD arguments while keeping `cat` as ENTRYPOINT.

## Expected result

The first container prints the build-time message; the second prints your greeting.

## Practice

Change `greeting.txt` and run the existing image before rebuilding. Success: you can explain why the old image still has the old content.

## Common errors and troubleshooting

COPY “not found”: check context directory and ignore rules. JSON form needs double quotes. A shell command passed after the image becomes an argument to `cat`, not a separate shell.

## Knowledge check

1. When does RUN execute?
2. When does CMD apply?
3. What remains when CMD is overridden?
4. Does COPY synchronize later edits?
5. What is the context?

> [!question]- Answers
> 1. During build.
> 2. At container startup, unless overridden.
> 3. The ENTRYPOINT executable.
> 4. No.
> 5. Files supplied to the builder.

## Cleanup

Containers remove themselves. `docker image rm lesson-cat` removes only the local lesson image tag when no container references it.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[01 - Docker Fundamentals/02 - Images and Containers|Images and Containers]]
- [[01 - Docker Fundamentals/04 - Docker Compose|Next in the roadmap: Docker Compose]]

## Official references

- [File official documentation](https://docs.docker.com/reference/dockerfile/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[01 - Docker Fundamentals/02 - Images and Containers\|Images and Containers]] | [[01 - Docker Fundamentals/04 - Docker Compose\|Docker Compose]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
