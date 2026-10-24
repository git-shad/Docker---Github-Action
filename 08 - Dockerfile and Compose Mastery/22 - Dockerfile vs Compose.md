---
title: "Dockerfile vs Compose"
subject: Docker
level: fundamentals
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - fundamentals
---

# Dockerfile vs Compose

Decide whether a change belongs to image construction or service runtime.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Decide whether a change belongs to image construction or service runtime.

## Questions answered

- What belongs in Dockerfile and what belongs in compose.yml?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/03 - FROM RUN CMD and ENTRYPOINT|FROM RUN CMD and ENTRYPOINT]], [[08 - Dockerfile and Compose Mastery/13 - Compose Build and Dockerfile Integration|Compose Build and Dockerfile Integration]], [[08 - Dockerfile and Compose Mastery/18 - Compose Commands and Entrypoints|Compose Commands and Entrypoints]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

Dockerfile defines a reusable image: runtime tools, source snapshots, built assets and startup defaults. Compose defines an application instance: services, build requests, runtime environment, mounts, networks and host ports. A single image can support app, worker and maintenance services by varying runtime configuration. Compose can request a build, but the recipe still defines the image.

Changing source copied into an image requires rebuilding and recreating containers to use the result. Changing a bind-mounted file may be visible immediately but the application may still need a reload. Editing Compose does not automatically mutate existing containers; up reconciles configuration. Restarting an existing container is not equivalent to rebuilding it.

## Syntax reference


| Need | Put it here | Scenario/reason |
| --- | --- | --- |
| Install PHP extension | Dockerfile RUN | The runtime must contain the compiled driver. |
| Default app command | Dockerfile CMD | The image should run without this particular Compose file. |
| Worker command override | Compose command | Reuse the same app image for another role. |
| Public HTTP host binding | Compose ports | Deployment host access varies by instance. |
| Database durable data | Compose volume mount | Data must outlive a container. |
| Non-secret build variant | Compose build.args plus Dockerfile ARG | Build choice differs from runtime environment. |


## Code examples

**Dockerfile:**
```dockerfile
FROM alpine:3.22
WORKDIR /app
RUN printf "image default\n" > message.txt
CMD ["cat", "/app/message.txt"]
```

**compose.yml:**
```yaml
services:
  report:
    build: .
    command: ["cat", "/etc/os-release"]
```

**Incorrect Dockerfile fragment:**
```dockerfile
ports: ["127.0.0.1:8080:80"]
```

This is YAML runtime configuration, not a Dockerfile instruction.

## Line-by-line walkthrough

1. The recipe creates content and a default cat command.

2. The service requests construction from the current context.

3. Its command overrides the image default without changing the image’s stored CMD.

4. Another service or docker run could still use the original message.

5. A local docker compose build would construct; docker compose run --rm report would instantiate the overridden command.

## DO ✅ / DON'T ❌

- **DO ✅** Ask which lifecycle owns a setting, because that decides rebuild versus recreation.
- **DO ✅** Keep useful image defaults, because the image may be used outside Compose.

- **DON'T ❌** Put runtime secrets into build instructions, because the artifact gets distributed.
- **DON'T ❌** Assume docker compose restart rebuilds source or applies edited environment, because restart reuses existing container configuration.

## Expected result

Every setting has a lifecycle and an explicit rebuild/recreation/reload requirement. This is an expected result, not an execution claim.

## Common errors and troubleshooting

Unknown Dockerfile instruction ports means syntax families were mixed. Unexpected old behavior can come from rebuilding without recreating, recreating without rebuilding, or using the wrong image tag.

## Practice exercise

**Problem and objective:** Classify ten settings from your own plan as build or runtime and state how changes apply.

**Success condition:** Every setting has a lifecycle and an explicit rebuild/recreation/reload requirement.

**Hint:** File contents and process configuration need separate reasoning.

## Knowledge check

1. Does Compose replace Dockerfile?
2. Can one image serve multiple roles?
3. Does Compose command change image CMD itself?
4. Does restart rebuild?
5. What publishes host ports?

> [!question]- Answers
> 1. No.
> 2. Yes.
> 3. No.
> 4. No.
> 5. Runtime configuration such as Compose ports.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/03 - FROM RUN CMD and ENTRYPOINT|FROM RUN CMD and ENTRYPOINT]]
- [[08 - Dockerfile and Compose Mastery/13 - Compose Build and Dockerfile Integration|Compose Build and Dockerfile Integration]]
- [[08 - Dockerfile and Compose Mastery/18 - Compose Commands and Entrypoints|Compose Commands and Entrypoints]]
- [[08 - Dockerfile and Compose Mastery/23 - Dockerfile and Compose Together|Dockerfile and Compose Together]]

## Official references

- [Overview documentation](https://docs.docker.com/compose/)
- [Build documentation](https://docs.docker.com/reference/compose-file/build/)
- [File documentation](https://docs.docker.com/reference/dockerfile/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/21 - Compose Anti-patterns\|Compose Anti-patterns]] | [[08 - Dockerfile and Compose Mastery/23 - Dockerfile and Compose Together\|Dockerfile and Compose Together]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
