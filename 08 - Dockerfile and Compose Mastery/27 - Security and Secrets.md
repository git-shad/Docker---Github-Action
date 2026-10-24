---
title: "Security and Secrets"
subject: Docker
level: advanced
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - advanced
---

# Security and Secrets

Choose build-time and runtime secret mechanisms and understand their limits.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Choose build-time and runtime secret mechanisms and understand their limits.

## Questions answered

- How do I avoid embedding credentials in images or tracked Compose files?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/02 - Dockerfile Instructions Reference|Dockerfile Instructions Reference]], [[08 - Dockerfile and Compose Mastery/05 - ARG ENV EXPOSE and VOLUME|ARG ENV EXPOSE and VOLUME]], [[08 - Dockerfile and Compose Mastery/06 - Build Context and Dockerignore|Build Context and Dockerignore]], [[08 - Dockerfile and Compose Mastery/12 - Compose Services and Images|Compose Services and Images]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

Secrets should not be COPY inputs, ARG/ENV values in image recipes, labels, logs, or browser bundle variables. A BuildKit secret mount grants a build step temporary access without intentionally committing the secret file into the layer. The command can still leak it through output or copied/generated files, so only trusted code should consume it. Secret content changes alone do not automatically invalidate the build cache.

Runtime Compose secrets grant selected services a file under /run/secrets by default. Local file-backed secrets are still host filesystem content, often bind-mounted; this does not automatically provide encrypted storage, secure distribution, or reliable uid/gid remapping. Applications need explicit file-reading support. A _FILE variable is an image convention, not universal Compose behavior. configs are for non-sensitive configuration, not a secret substitute.

## Syntax reference

Build: RUN --mount=type=secret,id=NAME,required=true. CLI/Compose must also supply the source. Runtime: top-level secrets declares a source, service secrets grants it. Keep sources out of both Git and context. Scope account credentials and rotate after exposure.

## Code examples

**Complete build-secret demonstration using only dummy training text in secrets/build_token.txt:**
```dockerfile
# syntax=docker/dockerfile:1
FROM alpine:3.22
RUN --mount=type=secret,id=build_token,required=true test -s /run/secrets/build_token
CMD ["printf", "Secret presence checked without embedding its text\n"]
```

**compose.yml:**
```yaml
services:
  check:
    build:
      context: .
      secrets: [build_token]
secrets:
  build_token:
    file: ./secrets/build_token.txt
```

**.dockerignore:**
```text
secrets
.env
.git
```

The explicit secret grant still supplies the selected file even though ordinary context inclusion excludes secrets/. This example verifies only file presence, not an authenticated download.

**Incorrect fragment:**
```dockerfile
COPY secrets/build_token.txt /tmp/token
RUN rm /tmp/token
```

Later deletion cannot erase earlier layers. Use the mount above and prevent the command from echoing or persisting secret contents.

## Line-by-line walkthrough

1. The syntax frontend enables supported mount handling.

2. The secret ID must match the build grant. required=true fails when unavailable.

3. test -s verifies nonempty content without printing it.

4. The mount is temporary for that RUN; the CMD prints only a fixed message.

5. Top-level source plus build grant is different from a service runtime grant. lesson 24 shows the runtime pattern and explicit Laravel wrapper.

6. Ignore rules protect ordinary context, while host file permissions and Git ignore policy protect other exposure paths.

## DO ✅ / DON'T ❌

- **DO ✅** Grant only required services/steps access, because unnecessary readers expand exposure.
- **DO ✅** Treat consuming build scripts as trusted code, because secret mounts cannot prevent intentional exfiltration by the consumer.

- **DON'T ❌** Believe a secret mechanism makes arbitrary build code safe, because it executes with access to the secret.
- **DON'T ❌** Pass backend credentials to a frontend compiler, because its output is public.

## Expected result

Your design leaves no secret in image configuration, Git, browser assets or logs, and states host-storage limitations. This is an expected result, not an execution claim.

## Common errors and troubleshooting

Missing secret ID: match declarations and grants. Permission denied: inspect actual file-backed UID/GID behavior. Cached authenticated step not rerunning after rotation: use an intentional non-secret cache-busting version when needed, without putting the secret itself in an argument.

## Practice exercise

**Problem and objective:** Design separate build and runtime grants, identifying which code reads each.

**Success condition:** Your design leaves no secret in image configuration, Git, browser assets or logs, and states host-storage limitations.

**Hint:** Separate source, grant, consumer and lifecycle in a four-column table.

## Knowledge check

1. Is ARG a secure secret channel?
2. Can a mounted-secret consumer leak it?
3. Are local file-backed secrets automatically encrypted?
4. Does every app support _FILE?
5. Does a secret content change automatically invalidate cache?

> [!question]- Answers
> 1. No.
> 2. Yes.
> 3. No.
> 4. No.
> 5. No.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/02 - Dockerfile Instructions Reference|Dockerfile Instructions Reference]]
- [[08 - Dockerfile and Compose Mastery/05 - ARG ENV EXPOSE and VOLUME|ARG ENV EXPOSE and VOLUME]]
- [[08 - Dockerfile and Compose Mastery/06 - Build Context and Dockerignore|Build Context and Dockerignore]]
- [[08 - Dockerfile and Compose Mastery/12 - Compose Services and Images|Compose Services and Images]]
- [[08 - Dockerfile and Compose Mastery/28 - Practice Exercises|Practice Exercises]]

## Official references

- [Secret documentation](https://docs.docker.com/build/building/secrets/)
- [Secrets documentation](https://docs.docker.com/reference/compose-file/secrets/)
- [Configs documentation](https://docs.docker.com/reference/compose-file/configs/)
- [Services documentation](https://docs.docker.com/reference/compose-file/services/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/26 - Debugging Docker Configurations\|Debugging Docker Configurations]] | [[08 - Dockerfile and Compose Mastery/28 - Practice Exercises\|Practice Exercises]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
