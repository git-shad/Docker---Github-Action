---
title: "COPY ADD WORKDIR and USER"
subject: Docker
level: fundamentals
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - fundamentals
---

# COPY ADD WORKDIR and USER

Choose file-transfer semantics and control paths and permissions.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Choose file-transfer semantics and control paths and permissions.

## Questions answered

- When should I use COPY, ADD, WORKDIR, and USER?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/02 - Dockerfile Instructions Reference|Dockerfile Instructions Reference]], [[08 - Dockerfile and Compose Mastery/03 - FROM RUN CMD and ENTRYPOINT|FROM RUN CMD and ENTRYPOINT]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

COPY includes explicit context files. ADD additionally supports behaviors such as unpacking recognized local tar archives and retrieving remote sources. Choose those extra semantics deliberately and verify remote integrity. WORKDIR avoids dependence on an inherited current directory. USER affects subsequent RUN operations and runtime startup; switching before root-only preparation can break installation.

A numeric USER does not create a login account or home directory. Image ownership does not override host bind-mount ownership. A runtime mount can hide files that COPY previously included. Non-root identity reduces privilege but cannot make a leaked secret or mounted Docker socket safe.

## Syntax reference


| Syntax | Purpose | Scenario |
| --- | --- | --- |
| COPY source dest | Ordinary file inclusion | Copy PHP source or package manifests. |
| ADD archive.tar /app/ | Recognized local archive extraction | Use only when extraction is intended. |
| WORKDIR /app | Create/select directory | Make relative paths predictable. |
| USER 10001:10001 | Set UID and GID | Run a read-only tool without root. |


## Code examples

**Complete Dockerfile; create message.txt first:**
```dockerfile
FROM alpine:3.22
WORKDIR /app
COPY --chown=10001:10001 --chmod=0444 message.txt ./message.txt
USER 10001:10001
CMD ["cat", "/app/message.txt"]
```

**Incorrect ordering fragment:**
```dockerfile
USER 10001:10001
RUN apk add --no-cache curl
```

OS package installation normally requires root. Prepare the image before switching USER; do not leave the application root merely to avoid the permission error.

## Line-by-line walkthrough

1. WORKDIR creates/selects an absolute path. Relative values can unintentionally combine with the parent image directory.

2. COPY reads from the context and sets explicit numeric ownership and file permissions.

3. The destination is relative to /app; named ownership would need user/group lookup in the image.

4. USER changes identity but does not create a user entry.

5. cat needs only read/traversal access, so no writable source directory is required.

## DO ✅ / DON'T ❌

- **DO ✅** Prefer COPY for ordinary source, because its behavior is narrower and easier to review.
- **DO ✅** Prepare narrow writable paths before USER, because applications may need caches without needing writable code.

- **DON'T ❌** Use chmod 777 as a blanket repair, because it grants unnecessary writes.
- **DON'T ❌** Assume image chown fixes bind mounts, because mounted host filesystem ownership still applies.

## Expected result

Your plan leaves source read-only and grants exactly the necessary output ownership. This is an expected result, not an execution claim.

## Common errors and troubleshooting

COPY cannot escape context with ../. Name-based --chown fails if the image has no matching account. A bind mount can replace correctly owned image files with incompatible host ownership.

## Practice exercise

**Problem and objective:** Design a tool that reads /app/source and writes only /app/output.

**Success condition:** Your plan leaves source read-only and grants exactly the necessary output ownership.

**Hint:** Create and chown the output directory during privileged build preparation.

## Knowledge check

1. Does USER create an account?
2. Does COPY synchronize later edits?
3. When choose ADD?
4. Does WORKDIR affect RUN?
5. Can mounts hide copied files?

> [!question]- Answers
> 1. No.
> 2. No.
> 3. When its extraction or remote-source semantics are specifically required and reviewed.
> 4. Yes.
> 5. Yes.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/02 - Dockerfile Instructions Reference|Dockerfile Instructions Reference]]
- [[08 - Dockerfile and Compose Mastery/03 - FROM RUN CMD and ENTRYPOINT|FROM RUN CMD and ENTRYPOINT]]
- [[08 - Dockerfile and Compose Mastery/05 - ARG ENV EXPOSE and VOLUME|ARG ENV EXPOSE and VOLUME]]

## Official references

- [File documentation](https://docs.docker.com/reference/dockerfile/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/03 - FROM RUN CMD and ENTRYPOINT\|FROM RUN CMD and ENTRYPOINT]] | [[08 - Dockerfile and Compose Mastery/05 - ARG ENV EXPOSE and VOLUME\|ARG ENV EXPOSE and VOLUME]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
