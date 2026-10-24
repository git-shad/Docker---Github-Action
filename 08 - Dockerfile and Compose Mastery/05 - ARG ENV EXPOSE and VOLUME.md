---
title: "ARG ENV EXPOSE and VOLUME"
subject: Docker
level: fundamentals
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - fundamentals
---

# ARG ENV EXPOSE and VOLUME

Separate build inputs, runtime configuration, port metadata, and storage declarations.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Separate build inputs, runtime configuration, port metadata, and storage declarations.

## Questions answered

- How do ARG, ENV, EXPOSE, and VOLUME differ?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/02 - Dockerfile Instructions Reference|Dockerfile Instructions Reference]], [[08 - Dockerfile and Compose Mastery/04 - COPY ADD WORKDIR and USER|COPY ADD WORKDIR and USER]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

ARG supplies a build parameter; ENV saves an image default used by later steps and runtime processes. A global ARG before FROM can parameterize the base but needs redeclaration within a stage for ordinary stage instructions. ENV of the same name takes precedence in relevant expansion. Neither is a secure secret channel: configuration, cache, history, or provenance can expose values.

EXPOSE documents an intended container listener but starts no process and opens no host binding. VOLUME declares a mount destination; without an explicit mount it can produce anonymous storage. Operators should choose named persistent volumes deliberately. BuildKit and legacy builders differ around writes after VOLUME, so do not depend on old builder-specific behavior for initialization.

## Syntax reference


| Choice | Lifetime/effect | Scenario |
| --- | --- | --- |
| ARG APP_FLAVOR | Build input, not automatically runtime env | Select a non-secret build variant. |
| ENV APP_MODE=local | Persistent runtime default | Set a safe application mode. |
| EXPOSE 8080 | Image port metadata | Document an actual HTTP listener. |
| Compose ports | Host runtime publication | Let a local browser reach container port 80. |
| VOLUME ["/data"] | Mount-path declaration | Advertise storage intent; operators choose its identity. |


## Code examples

**Complete metadata demonstration:**
```dockerfile
ARG BASE=alpine:3.22
FROM ${BASE}
ARG APP_FLAVOR=training
ENV APP_MODE=${APP_FLAVOR}
WORKDIR /data
EXPOSE 8080/tcp
VOLUME ["/data"]
CMD ["sh", "-c", "printf \"mode=%s\\n\" \"$APP_MODE\""]
```

**Incorrect secret-channel fragment:**
```dockerfile
ARG DATABASE_PASSWORD
ENV DATABASE_PASSWORD=${DATABASE_PASSWORD}
```

This promotes build input into image configuration. Use build-secret mounts for build access and runtime secret delivery for runtime access.

## Line-by-line walkthrough

1. BASE is global and can select FROM.

2. APP_FLAVOR is declared in the stage so ENV can use it.

3. ENV persists APP_MODE; runtime configuration can override it.

4. EXPOSE documents a port, but this demonstration has no HTTP server.

5. VOLUME does not choose a stable named volume or host path.

6. The CMD explicitly invokes a shell to expand APP_MODE; direct exec does not expand it.

## DO ✅ / DON'T ❌

- **DO ✅** Keep ARG and ENV non-sensitive, because metadata is distributable.
- **DO ✅** Choose named mounts in Compose for durable data, because anonymous volumes make ownership and cleanup harder.

- **DON'T ❌** Assume EXPOSE publishes a port, because host binding is a separate runtime action.
- **DON'T ❌** Treat container removal as equivalent to volume deletion, because named data has an independent lifecycle.

## Expected result

Your explanation gives image default review and runtime value demo without treating either as a secret. This is an expected result, not an execution claim.

## Common errors and troubleshooting

An empty build value can mean ARG scope is wrong. A published port with no listener still fails. An image VOLUME can create unexpected anonymous resources when the operator supplies no mount.

## Practice exercise

**Problem and objective:** Predict build APP_FLAVOR=review followed by runtime APP_MODE=demo.

**Success condition:** Your explanation gives image default review and runtime value demo without treating either as a secret.

**Hint:** Draw builder scope separately from container environment.

## Knowledge check

1. Does ARG automatically persist as environment?
2. Which stores a process default?
3. Does EXPOSE publish?
4. Does VOLUME choose a volume name?
5. Can global ARG need redeclaration?

> [!question]- Answers
> 1. No.
> 2. ENV.
> 3. No.
> 4. No.
> 5. Yes, for use inside a stage.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/02 - Dockerfile Instructions Reference|Dockerfile Instructions Reference]]
- [[08 - Dockerfile and Compose Mastery/04 - COPY ADD WORKDIR and USER|COPY ADD WORKDIR and USER]]
- [[08 - Dockerfile and Compose Mastery/06 - Build Context and Dockerignore|Build Context and Dockerignore]]

## Official references

- [File documentation](https://docs.docker.com/reference/dockerfile/)
- [Secret documentation](https://docs.docker.com/build/building/secrets/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/04 - COPY ADD WORKDIR and USER\|COPY ADD WORKDIR and USER]] | [[08 - Dockerfile and Compose Mastery/06 - Build Context and Dockerignore\|Build Context and Dockerignore]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
