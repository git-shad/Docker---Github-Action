---
title: "Compose Commands and Entrypoints"
subject: Docker
level: intermediate
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - intermediate
---

# Compose Commands and Entrypoints

Predict the runtime override of image command, entrypoint, directory, and user.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Predict the runtime override of image command, entrypoint, directory, and user.

## Questions answered

- How do Compose command and entrypoint differ from Dockerfile CMD and ENTRYPOINT?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/17 - Compose Depends On and Healthchecks|Compose Depends On and Healthchecks]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

Compose command replaces image CMD for the created container. Compose entrypoint replaces image ENTRYPOINT; a non-null entrypoint means image CMD is ignored. null inherits, while an empty list or empty string can clear the default. Supply the complete intended pair when overriding both. Compose command strings do not automatically inherit Dockerfile SHELL semantics, so invoke sh explicitly for shell operators or expansion.

working_dir and user affect runtime, not image construction. init can help forward signals and reap children but does not become a general-purpose process supervisor. Dockerfile startup remains useful as an image default even if a particular Compose service overrides it.

## Syntax reference


| Setting | Replaces | Scenario |
| --- | --- | --- |
| command | Image CMD | Use the same PHP image for a queue worker. |
| entrypoint | Image ENTRYPOINT and ignore default CMD when non-null | Run a diagnostic executable instead of the packaged app. |
| docker compose up | Create/reconcile defined services | Run a local stack. |
| docker compose run | Create a one-off service-based container | Run migrations or Composer; host ports are not published by default. |


## Code examples

**Complete compose.yml:**
```yaml
services:
  inspect:
    image: alpine:3.22
    entrypoint: ["cat"]
    command: ["/etc/os-release"]
    working_dir: /tmp
    user: "10001:10001"
    init: true
```

**Incorrect shell expectation fragment:**
```yaml
command: ["echo", "$$HOME"]
```

Compose can pass the dollar through, but direct echo receives a literal variable name. Correct with an explicit shell when expansion is intended:
```yaml
command: ["sh", "-c", "printf \"%s\\n\" \"$$HOME\""]
```


## Line-by-line walkthrough

1. entrypoint chooses cat.

2. command supplies one argument; final argv is cat /etc/os-release.

3. working_dir changes runtime cwd only.

4. user chooses numeric identity; the readable system file needs no privileged access.

5. init adds the supported init wrapper; the process still must handle its own application shutdown.

6. The shell correction is a separate fragment for an image without a conflicting fixed cat entrypoint; clear or replace that entrypoint if combining it with this service.

## DO ✅ / DON'T ❌

- **DO ✅** Write out final argv, because override combinations can be counterintuitive.
- **DO ✅** Use explicit shells only when needed, because direct execution simplifies signal and quoting behavior.

- **DON'T ❌** Use command as if it were Dockerfile RUN, because it executes every container startup rather than during build.
- **DON'T ❌** Assume docker compose run publishes service ports, because one-off commands normally do not need them.

## Expected result

Your configuration preserves its intended entrypoint and supplies the correct worker argv. This is an expected result, not an execution claim.

## Common errors and troubleshooting

“cat: sh: No such file” often means a fixed entrypoint received shell tokens as filenames. An empty command may accidentally remove all startup arguments. Inspect resolved config and image metadata.

## Practice exercise

**Problem and objective:** Turn a PHP app image into a worker service on paper using command only.

**Success condition:** Your configuration preserves its intended entrypoint and supplies the correct worker argv.

**Hint:** Read base image ENTRYPOINT before overriding CMD.

## Knowledge check

1. What does command replace?
2. What does non-null entrypoint do to default CMD?
3. Does null clear a default?
4. Does working_dir change COPY context?
5. Does init replace correct app shutdown?

> [!question]- Answers
> 1. CMD.
> 2. It is ignored.
> 3. No, it inherits.
> 4. No.
> 5. No.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/17 - Compose Depends On and Healthchecks|Compose Depends On and Healthchecks]]
- [[08 - Dockerfile and Compose Mastery/19 - Compose Profiles and Overrides|Compose Profiles and Overrides]]

## Official references

- [Services documentation](https://docs.docker.com/reference/compose-file/services/)
- [File documentation](https://docs.docker.com/reference/dockerfile/)
- [Cli documentation](https://docs.docker.com/reference/cli/docker/compose/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/17 - Compose Depends On and Healthchecks\|Compose Depends On and Healthchecks]] | [[08 - Dockerfile and Compose Mastery/19 - Compose Profiles and Overrides\|Compose Profiles and Overrides]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
