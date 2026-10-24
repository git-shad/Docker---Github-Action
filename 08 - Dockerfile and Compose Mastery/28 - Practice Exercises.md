---
title: "Practice Exercises"
subject: Docker
level: intermediate
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - intermediate
---

# Practice Exercises

Write configurations independently through 15 increasingly demanding exercises.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Write configurations independently through 15 increasingly demanding exercises.

## Questions answered

- How can I practice writing Dockerfiles and Compose files rather than only reading examples?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/01 - Dockerfile Syntax Fundamentals|Dockerfile Syntax Fundamentals]], [[08 - Dockerfile and Compose Mastery/03 - FROM RUN CMD and ENTRYPOINT|FROM RUN CMD and ENTRYPOINT]], [[08 - Dockerfile and Compose Mastery/04 - COPY ADD WORKDIR and USER|COPY ADD WORKDIR and USER]], [[08 - Dockerfile and Compose Mastery/05 - ARG ENV EXPOSE and VOLUME|ARG ENV EXPOSE and VOLUME]], [[08 - Dockerfile and Compose Mastery/06 - Build Context and Dockerignore|Build Context and Dockerignore]], [[08 - Dockerfile and Compose Mastery/07 - Docker Layer Caching|Docker Layer Caching]], [[08 - Dockerfile and Compose Mastery/08 - Multi-stage Builds|Multi-stage Builds]], [[08 - Dockerfile and Compose Mastery/11 - Compose YAML Syntax Fundamentals|Compose YAML Syntax Fundamentals]], [[08 - Dockerfile and Compose Mastery/13 - Compose Build and Dockerfile Integration|Compose Build and Dockerfile Integration]], [[08 - Dockerfile and Compose Mastery/14 - Compose Ports and Networks|Compose Ports and Networks]], [[08 - Dockerfile and Compose Mastery/15 - Compose Volumes and Bind Mounts|Compose Volumes and Bind Mounts]], [[08 - Dockerfile and Compose Mastery/16 - Compose Environment Variables|Compose Environment Variables]], [[08 - Dockerfile and Compose Mastery/17 - Compose Depends On and Healthchecks|Compose Depends On and Healthchecks]], [[08 - Dockerfile and Compose Mastery/19 - Compose Profiles and Overrides|Compose Profiles and Overrides]], [[08 - Dockerfile and Compose Mastery/23 - Dockerfile and Compose Together|Dockerfile and Compose Together]], [[08 - Dockerfile and Compose Mastery/24 - Laravel React MariaDB Example|Laravel React MariaDB Example]], [[08 - Dockerfile and Compose Mastery/25 - Development vs Production|Development vs Production]], [[08 - Dockerfile and Compose Mastery/26 - Debugging Docker Configurations|Debugging Docker Configurations]], [[08 - Dockerfile and Compose Mastery/27 - Security and Secrets|Security and Secrets]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

Learning requires predicting behavior, writing a solution, then comparing against a reasoned answer. These exercises move from a basic image through packages, working directories, source inclusion, argv, services, networking, persistence, environment, readiness, stages, integration, environment separation and diagnosis. The expected results describe what successful future practice would show, not an assertion that a command passed.

Solutions are collapsed so you can attempt the design first. A correct answer may differ from the sample if it preserves the requirements and explains trade-offs. Focus on why a setting belongs to its lifecycle and what evidence would establish correctness.

## Syntax reference

Use each problem’s exact filename and context. Configuration snippets for intentionally wrong cases are labeled. Optional command blocks are shown as documentation and must not be run against an unrelated project.

## Code examples

**Planning template before you write configuration:**
```text
Program and executable:
Required source/build inputs:
Build-time non-secret parameters:
Runtime configuration and secrets:
Ports and network peers:
Persistent data and ownership:
Readiness and failure behavior:
Expected observable result:
Cleanup and recovery boundaries:
```

**Poor approach:** copying a large stack before identifying any of these requirements makes errors harder to localize.

## Line-by-line walkthrough

1. Complete the planning template for the selected exercise.

2. Write files in your own practice notes before revealing the solution.

3. Predict the result and at least one failure condition.

4. Open the collapsed solution and compare lifecycle, syntax and scope, not just spelling.

5. If later choosing to execute, validate only in your disposable lab with all stated prerequisites; record actual evidence separately from predictions.

## DO ✅ / DON'T ❌

- **DO ✅** Explain every line, because memorized snippets fail when paths or roles change.
- **DO ✅** Keep failed hypotheses in your practice notes, because they reveal what your mental model missed.

- **DON'T ❌** Treat matching the sample as proof the application works, because configuration reasoning and actual behavior need separate evidence.
- **DON'T ❌** Use real credentials or production data for an exercise, because the examples are intentionally disposable.

## Expected result

For every task you can describe the expected result, a likely failure, and why the sample solution works. This is an expected result, not an execution claim.

## Common errors and troubleshooting

If stuck, reduce to the smallest earlier working concept. Do not add privileges, public ports, arbitrary sleeps or volume deletion without a demonstrated requirement.

## Practice exercise

**Problem and objective:** Complete all 15 tasks before the final assessment.

**Success condition:** For every task you can describe the expected result, a likely failure, and why the sample solution works.

**Hint:** Attempt first, then expand the callout.

## Knowledge check

1. Why hide solutions?
2. Can a different solution be valid?
3. Do expected results prove execution?
4. What precedes configuration?
5. Where should practice data live?

> [!question]- Answers
> 1. To practice recall and design first.
> 2. Yes, if requirements and reasoning hold.
> 3. No.
> 4. A requirements and lifecycle plan.
> 5. Only in a deliberately disposable lab.

## Fifteen progressive exercises

Try each problem before opening its solution callout. Solutions are complete for the stated lab inputs, not copies of your real projects. No exercises were executed. If later practicing, choose a different directory/project per exercise, validate the model first, and inspect the cleanup notes before creating resources. Keep any supplied local passwords out of Git and never use production data.

### Exercise 01 — Basic Dockerfile

**Problem:** Write a recipe that prints the container OS without copying files.

**Learning objectives:** Select a base and define runtime argv.

**Expected result:** A future run prints Alpine OS metadata.

**Hints:** cat is supplied by Alpine.

> [!example]- Full solution and explanation
> **Full solution — Dockerfile:**
> ```dockerfile
> FROM alpine:3.22
> CMD ["cat", "/etc/os-release"]
> ```
>
> **Line-by-line explanation:**
>
> 1. FROM selects the userspace and cat executable.
> 2. CMD stores the direct command and one path argument.
>
> **Common mistakes:** Using RUN cat would print during construction rather than define the runtime task.
>
> **Optional challenge:** Change the default argument while preserving a fixed cat ENTRYPOINT.

### Exercise 02 — Install a package with RUN

**Problem:** Create an image that can report curl version.

**Learning objectives:** Use the package manager appropriate to the base and preserve failure status.

**Expected result:** A future run prints curl version from the image.

**Hints:** Update/install/cleanup belong to the same related build step.

> [!example]- Full solution and explanation
> **Full solution — Dockerfile:**
> ```dockerfile
> FROM debian:bookworm-slim
> RUN apt-get update \
>     && apt-get install -y --no-install-recommends curl ca-certificates \
>     && rm -rf /var/lib/apt/lists/*
> CMD ["curl", "--version"]
> ```
>
> **Line-by-line explanation:**
>
> 1. FROM selects Debian, so apt rather than apk is appropriate.
> 2. update obtains indexes; && stops on failure.
> 3. install includes only required packages and avoids optional recommendations.
> 4. Cleanup removes package index files in the same layer; CMD runs the tool later.
>
> **Common mistakes:** Using apk on Debian or splitting update from install without cache reasoning is incorrect.
>
> **Optional challenge:** Explain when the base and package versions would need deliberate refresh.

### Exercise 03 — Working directory

**Problem:** Make a runtime command read a relative file from /lesson.

**Learning objectives:** Understand persistent WORKDIR versus a temporary shell cd.

**Expected result:** A future run prints path-aware.

**Hints:** WORKDIR applies to later RUN and default runtime cwd.

> [!example]- Full solution and explanation
> **Full solution — Dockerfile:**
> ```dockerfile
> FROM alpine:3.22
> WORKDIR /lesson
> RUN printf "path-aware\n" > result.txt
> CMD ["cat", "result.txt"]
> ```
>
> **Line-by-line explanation:**
>
> 1. WORKDIR creates/selects /lesson.
> 2. RUN writes result.txt there.
> 3. CMD reads it relative to the same configured working directory.
>
> **Common mistakes:** RUN cd /lesson in a separate instruction does not set later cwd.
>
> **Optional challenge:** Replace the relative runtime path with an absolute one and compare behavior.

### Exercise 04 — Copy an application

**Problem:** Include a tiny PHP script in a non-root image.

**Learning objectives:** Control the source snapshot, destination, and identity.

**Expected result:** A future run prints copied application.

**Hints:** Create the companion script in context; COPY cannot read outside it.

> [!example]- Full solution and explanation
> **Full solution — Dockerfile:**
> ```dockerfile
> FROM php:8.4-cli
> WORKDIR /app
> COPY hello.php ./hello.php
> USER 10001:10001
> CMD ["php", "/app/hello.php"]
> ```
>
> **hello.php:**
> ```php
> <?php
> echo "copied application\n";
> ```
>
> **Line-by-line explanation:**
>
> 1. FROM supplies PHP CLI.
> 2. WORKDIR sets /app.
> 3. COPY snapshots the companion file.
> 4. USER lowers privilege; the file is readable.
> 5. CMD executes the script.
>
> **Common mistakes:** Copying from an unrelated host absolute path or forgetting the companion file causes failure.
>
> **Optional challenge:** Add a dedicated writable output directory without granting source writes.

### Exercise 05 — Startup commands

**Problem:** Make a command image whose default file can be replaced.

**Learning objectives:** Combine ENTRYPOINT and CMD and predict final argv.

**Expected result:** Default reads the generated message; an alternate path replaces only that argument.

**Hints:** ENTRYPOINT is the tool and CMD is its default argument.

> [!example]- Full solution and explanation
> **Full solution — Dockerfile:**
> ```dockerfile
> FROM alpine:3.22
> RUN printf "startup\n" > /message.txt
> ENTRYPOINT ["cat"]
> CMD ["/message.txt"]
> ```
>
> **Line-by-line explanation:**
>
> 1. RUN writes during build.
> 2. ENTRYPOINT fixes cat.
> 3. CMD supplies the replaceable file path.
>
> **Common mistakes:** Two CMD instructions do not start two processes; only the last applies.
>
> **Optional challenge:** Explain the difference between an argument override and an explicit entrypoint override.

### Exercise 06 — First compose.yml

**Problem:** Describe a one-off service without a custom image.

**Learning objectives:** Write services, image, and command with correct nesting.

**Expected result:** The selected process reports the OS and exits normally.

**Hints:** services contains a mapping, not a list.

> [!example]- Full solution and explanation
> **Full solution — compose.yml:**
> ```yaml
> services:
>   identify:
>     image: alpine:3.22
>     command: ["cat", "/etc/os-release"]
> ```
>
> **Line-by-line explanation:**
>
> 1. services is top-level.
> 2. identify is the service key.
> 3. image selects a known artifact.
> 4. command replaces its startup default.
>
> **Common mistakes:** Placing image at the same indentation as identify creates the wrong model.
>
> **Optional challenge:** Add a second service using the same image with a different command.

### Exercise 07 — Multiple services

**Problem:** Define a web service and an optional diagnostic peer.

**Learning objectives:** Separate roles while sharing the default project network.

**Expected result:** When web is ready, a one-off client can read its HTTP page.

**Hints:** The client uses web:80; it is not a host browser.

> [!example]- Full solution and explanation
> **Full solution — compose.yml:**
> ```yaml
> services:
>   web:
>     image: nginx:stable-alpine
>     ports: ["127.0.0.1:8080:80"]
>   client:
>     image: alpine:3.22
>     profiles: [tools]
>     command: ["wget", "-qO-", "http://web:80/"]
> ```
>
> **Line-by-line explanation:**
>
> 1. web is the HTTP role and only host-published entry point.
> 2. client is a separate one-off process.
> 3. Both attach to the implicit default network.
> 4. The profile keeps diagnostics optional.
>
> **Common mistakes:** The file does not guarantee readiness: start web and verify it before running client; lesson 11 below adds a bounded health gate.
>
> **Optional challenge:** Remove host publication and explain why the peer request can still work.

### Exercise 08 — Explicit network

**Problem:** Attach only the needed peers to a named network.

**Learning objectives:** Understand service DNS independent of host publication.

**Expected result:** Both peers share backend and the client resolves web.

**Hints:** Declare the logical network at top level and attach both services.

> [!example]- Full solution and explanation
> **Full solution — compose.yml:**
> ```yaml
> services:
>   web:
>     image: nginx:stable-alpine
>     networks: [backend]
>   client:
>     image: alpine:3.22
>     networks: [backend]
>     profiles: [tools]
>     command: ["wget", "-qO-", "http://web:80/"]
> networks:
>   backend: {}
> ```
>
> **Line-by-line explanation:**
>
> 1. The network resource is declared once.
> 2. Each service attachment grants network membership.
> 3. No published port is necessary for the peer request.
>
> **Common mistakes:** A network name alone does not set internal isolation, and DNS resolution is not readiness.
>
> **Optional challenge:** Design a third service that deliberately cannot reach this network.

### Exercise 09 — Persistent MariaDB

**Problem:** Preserve database data independently of its container.

**Learning objectives:** Use the correct data destination and required local configuration.

**Expected result:** A table created in lesson survives container recreation with the same project volume.

**Hints:** MariaDB stores this image’s data at /var/lib/mysql.

> [!example]- Full solution and explanation
> **Full solution — compose.yml:**
> ```yaml
> services:
>   db:
>     image: mariadb:11.4
>     environment:
>       MARIADB_DATABASE: lesson
>       MARIADB_ROOT_PASSWORD: "${LAB_DB_ROOT_PASSWORD:?Supply a disposable lab password}"
>     volumes: ["db-data:/var/lib/mysql"]
> volumes:
>   db-data: {}
> ```
>
> **Line-by-line explanation:**
>
> 1. The image initializes lesson only for a new data directory.
> 2. The required variable avoids a tracked password literal; this is lab environment delivery, not encrypted storage.
> 3. The mount preserves the actual data path.
> 4. The top-level volume gives managed storage a stable project identity.
>
> **Common mistakes:** Changing initialization env does not reset existing credentials; down --volumes destroys the persistence you are trying to demonstrate.
>
> **Optional challenge:** Design a separate restricted app user and a file-secret password source.

### Exercise 10 — Runtime variables

**Problem:** Pass a non-secret mode through Compose and print it from a shell.

**Learning objectives:** Separate host interpolation from container expansion.

**Expected result:** With no host APP_MODE a future run prints local; with APP_MODE=review it prints review.

**Hints:** Use $$ for the container-side dollar.

> [!example]- Full solution and explanation
> **Full solution — compose.yml:**
> ```yaml
> services:
>   report:
>     image: alpine:3.22
>     environment:
>       APP_MODE: "${APP_MODE:-local}"
>     command: ["sh", "-c", "printf \"%s\\n\" \"$$APP_MODE\""]
> ```
>
> **Line-by-line explanation:**
>
> 1. The host interpolation expression chooses a value.
> 2. environment injects it into the container.
> 3. Explicit sh expands the escaped dollar at runtime.
>
> **Common mistakes:** Direct exec echo does not expand a variable; interpolation .env does not automatically import every key.
>
> **Optional challenge:** Use a required nonempty label and predict resolution failure when unset.

### Exercise 11 — Readiness checks

**Problem:** Declare HTTP health and wait for it before a client starts.

**Learning objectives:** Separate health measurement from dependency ordering.

**Expected result:** The client starts only after nginx passes its declared HTTP probe.

**Hints:** Use a command already available in the nginx Alpine image.

> [!example]- Full solution and explanation
> **Full solution — compose.yml:**
> ```yaml
> services:
>   web:
>     image: nginx:stable-alpine
>     healthcheck:
>       test: ["CMD-SHELL", "wget -q -O /dev/null http://127.0.0.1:80/ || exit 1"]
>       interval: 5s
>       timeout: 3s
>       retries: 12
>   client:
>     image: alpine:3.22
>     depends_on:
>       web:
>         condition: service_healthy
>     command: ["wget", "-qO-", "http://web:80/"]
> ```
>
> **Line-by-line explanation:**
>
> 1. The probe requests the local server and returns nonzero on HTTP/connection failure.
> 2. Timing bounds repeated attempts.
> 3. The dependency condition gates initial client startup.
> 4. The client’s request is a second observable action, not the healthcheck itself.
>
> **Common mistakes:** Short depends_on is not equivalent; unhealthy later does not automatically restart the server.
>
> **Optional challenge:** Define what to do when a dependency fails after the client has started.

### Exercise 12 — Multi-stage artifact

**Problem:** Use Node during construction but serve output with nginx.

**Learning objectives:** Copy only intentional output across stage boundaries.

**Expected result:** The final filesystem serves a generated static page and contains no Node toolchain from the first stage.

**Hints:** This is a self-contained artifact lesson, not a React application compiler.

> [!example]- Full solution and explanation
> **Full solution — Dockerfile:**
> ```dockerfile
> FROM node:22-bookworm-slim AS generate
> RUN mkdir -p /out
> RUN node -e "require('fs').writeFileSync('/out/index.html', '<h1>multi-stage lesson</h1>')"
> FROM nginx:stable-alpine
> COPY --from=generate /out/index.html /usr/share/nginx/html/index.html
> ```
>
> **Line-by-line explanation:**
>
> 1. The named stage supplies Node.
> 2. The directory creation is build-time filesystem preparation.
> 3. Node writes a deterministic static artifact.
> 4. The final FROM starts a separate runtime.
> 5. COPY transfers only that artifact.
>
> **Common mistakes:** COPY from the host instead of --from reads the wrong filesystem.
>
> **Optional challenge:** Replace the generator with an actual locked React TypeScript build using lesson 8.

### Exercise 13 — Combine recipe and service

**Problem:** Create a custom report image and run it through Compose.

**Learning objectives:** Connect context, image defaults and runtime settings.

**Expected result:** The service prints combined rather than its image default local.

**Hints:** Keep all three companion files in one lab directory.

> [!example]- Full solution and explanation
> **Full solution — Dockerfile:**
> ```dockerfile
> FROM alpine:3.22
> WORKDIR /app
> COPY --chmod=0555 report.sh ./report.sh
> ENV APP_MODE=local
> USER 10001:10001
> CMD ["/app/report.sh"]
> ```
>
> **report.sh:**
> ```bash
> #!/bin/sh
> set -eu
> printf "%s\n" "$APP_MODE"
> ```
>
> **compose.yml:**
> ```yaml
> services:
>   report:
>     build: .
>     environment:
>       APP_MODE: combined
> ```
>
> **Line-by-line explanation:**
>
> 1. The recipe provides a readable/executable script.
> 2. ENV establishes a standalone default.
> 3. USER reduces runtime privilege.
> 4. Compose build requests construction; its environment overrides the default at creation.
>
> **Common mistakes:** Editing the recipe without rebuilding does not modify an old image.
>
> **Optional challenge:** Give a second service a different mode while reusing the same built image.

### Exercise 14 — Separate dev and release

**Problem:** Keep a base image-only web service and add source construction through a dev file.

**Learning objectives:** Select exact file sets and understand overlay scope.

**Expected result:** The base consumes a chosen artifact; the overlay builds the local static source deliberately.

**Hints:** Use a required WEB_IMAGE value even for a development tag.

> [!example]- Full solution and explanation
> **Full solution — compose.yml:**
> ```yaml
> services:
>   web:
>     image: "${WEB_IMAGE:?Set a selected image reference}"
>     ports: ["127.0.0.1:8080:80"]
>     restart: unless-stopped
> ```
>
> **compose.dev.yml:**
> ```yaml
> services:
>   web:
>     build: .
>     pull_policy: build
> ```
>
> **Dockerfile:**
> ```dockerfile
> FROM nginx:stable-alpine
> COPY index.html /usr/share/nginx/html/index.html
> ```
>
> **index.html:**
> ```html
> <h1>development artifact</h1>
> ```
>
> **Non-secret host configuration for a later local demonstration:**
> ```bash
> export WEB_IMAGE=mastery-static:dev
> docker compose -f compose.yml -f compose.dev.yml config --quiet
> ```
>
> **Line-by-line explanation:**
>
> 1. The base names a selected artifact rather than an arbitrary checkout.
> 2. The loopback publication is suitable for a local demo or a separately configured host proxy.
> 3. The dev overlay adds build context and pull_policy.
> 4. The optional explicit -f selection makes the chosen environment reviewable.
>
> **Common mistakes:** A prod filename alone supplies no TLS, backups, monitoring, approval, or rollout guarantees.
>
> **Optional challenge:** Promote an actual tested digest and write a rollback compatibility record.

### Exercise 15 — Diagnose an intentionally broken model

**Problem:** Repair a peer request that incorrectly targets its own localhost and a host-only port.

**Learning objectives:** Separate DNS, listener ports, and readiness.

**Expected result:** The corrected client uses web:80 on a shared network and waits for server health.

**Hints:** Use the final solution rather than weakening host exposure.

**Intentionally broken command fragment; do not treat as a standalone solution:**
```yaml
command: ["wget", "-qO-", "http://localhost:8080/"]
```

> [!example]- Full solution and explanation
> **Full solution — compose.yml:**
> ```yaml
> services:
>   web:
>     image: nginx:stable-alpine
>     healthcheck:
>       test: ["CMD-SHELL", "wget -q -O /dev/null http://127.0.0.1/ || exit 1"]
>       interval: 5s
>       timeout: 3s
>       retries: 12
>   client:
>     image: alpine:3.22
>     depends_on:
>       web:
>         condition: service_healthy
>     command: ["wget", "-qO-", "http://web:80/"]
> ```
>
> **Line-by-line explanation:**
>
> 1. The faulty command shown before the solution addresses client itself.
> 2. The server probe measures local HTTP readiness.
> 3. service_healthy gates client startup.
> 4. The repaired URL uses network DNS and the server’s container listener.
>
> **Common mistakes:** Publishing another port or deleting a volume cannot fix the wrong peer hostname.
>
> **Optional challenge:** Explain what additional evidence is needed to diagnose HTTP 401 after networking is fixed.



## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/01 - Dockerfile Syntax Fundamentals|Dockerfile Syntax Fundamentals]]
- [[08 - Dockerfile and Compose Mastery/03 - FROM RUN CMD and ENTRYPOINT|FROM RUN CMD and ENTRYPOINT]]
- [[08 - Dockerfile and Compose Mastery/04 - COPY ADD WORKDIR and USER|COPY ADD WORKDIR and USER]]
- [[08 - Dockerfile and Compose Mastery/05 - ARG ENV EXPOSE and VOLUME|ARG ENV EXPOSE and VOLUME]]
- [[08 - Dockerfile and Compose Mastery/06 - Build Context and Dockerignore|Build Context and Dockerignore]]
- [[08 - Dockerfile and Compose Mastery/07 - Docker Layer Caching|Docker Layer Caching]]
- [[08 - Dockerfile and Compose Mastery/08 - Multi-stage Builds|Multi-stage Builds]]
- [[08 - Dockerfile and Compose Mastery/11 - Compose YAML Syntax Fundamentals|Compose YAML Syntax Fundamentals]]
- [[08 - Dockerfile and Compose Mastery/13 - Compose Build and Dockerfile Integration|Compose Build and Dockerfile Integration]]
- [[08 - Dockerfile and Compose Mastery/14 - Compose Ports and Networks|Compose Ports and Networks]]
- [[08 - Dockerfile and Compose Mastery/15 - Compose Volumes and Bind Mounts|Compose Volumes and Bind Mounts]]
- [[08 - Dockerfile and Compose Mastery/16 - Compose Environment Variables|Compose Environment Variables]]
- [[08 - Dockerfile and Compose Mastery/17 - Compose Depends On and Healthchecks|Compose Depends On and Healthchecks]]
- [[08 - Dockerfile and Compose Mastery/19 - Compose Profiles and Overrides|Compose Profiles and Overrides]]
- [[08 - Dockerfile and Compose Mastery/23 - Dockerfile and Compose Together|Dockerfile and Compose Together]]
- [[08 - Dockerfile and Compose Mastery/24 - Laravel React MariaDB Example|Laravel React MariaDB Example]]
- [[08 - Dockerfile and Compose Mastery/25 - Development vs Production|Development vs Production]]
- [[08 - Dockerfile and Compose Mastery/26 - Debugging Docker Configurations|Debugging Docker Configurations]]
- [[08 - Dockerfile and Compose Mastery/27 - Security and Secrets|Security and Secrets]]
- [[08 - Dockerfile and Compose Mastery/29 - Common Errors and Solutions|Common Errors and Solutions]]

## Official references

- [File documentation](https://docs.docker.com/reference/dockerfile/)
- [Compose documentation](https://docs.docker.com/reference/compose-file/)
- [Build documentation](https://docs.docker.com/reference/compose-file/build/)
- [Services documentation](https://docs.docker.com/reference/compose-file/services/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/27 - Security and Secrets\|Security and Secrets]] | [[08 - Dockerfile and Compose Mastery/29 - Common Errors and Solutions\|Common Errors and Solutions]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
