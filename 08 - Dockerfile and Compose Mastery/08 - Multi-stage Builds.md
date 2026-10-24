---
title: "Multi-stage Builds"
subject: Docker
level: advanced
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - advanced
---

# Multi-stage Builds

Separate compilation tools from runtime content.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Separate compilation tools from runtime content.

## Questions answered

- How do I design a multi-stage frontend build?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/07 - Docker Layer Caching|Docker Layer Caching]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

A stage begins with FROM. Name it so artifact references remain clear when the recipe changes. A final stage starting from a different base does not automatically inherit previous ENV, USER, tools, or source. Only intentional stage inheritance or copied files cross that boundary. BuildKit can skip unrelated stages when a target is selected.

Node compiles static React assets; nginx serves them. Laravel instead needs a PHP runtime and extensions: static nginx alone cannot execute PHP. Native build output must be compatible with final architecture and libraries.

## Syntax reference

FROM image AS name declares a stage. COPY --from=name /source /destination copies its files. build.target or docker build --target selects a stage.

## Code examples

**Complete recipe for an existing Vite React TypeScript project:**
```dockerfile
# syntax=docker/dockerfile:1
FROM node:22-bookworm-slim AS assets
WORKDIR /src
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
RUN npm run typecheck && npm run build
FROM nginx:stable-alpine AS web
COPY --from=assets /src/dist/ /usr/share/nginx/html/
EXPOSE 80
```

Requires typecheck/build scripts, package-lock.json, output dist, and .dockerignore. Frontend build variables are public.

**Incorrect path fragment:**
```dockerfile
COPY --from=assets /dist/ /usr/share/nginx/html/
```

A normal Vite build under /src writes /src/dist, not /dist.

## Line-by-line walkthrough

1. The assets stage installs locked Node dependencies.

2. Typecheck is a project-defined script; bundling alone may not validate types.

3. The second FROM starts an independent nginx filesystem and inherited startup.

4. Only dist crosses stages; Node tools and source need not ship.

5. EXPOSE documents HTTP port 80. Nested SPA routes need a deliberate fallback config.

## DO ✅ / DON'T ❌

- **DO ✅** Name stages for their responsibility, because numeric references become fragile after edits.
- **DO ✅** Inspect final contents and runtime behavior, because compiling successfully is not the same as serving successfully.

- **DON'T ❌** Copy the entire build tree into the runtime, because that can reintroduce dependencies and secrets.
- **DON'T ❌** Assume a frontend variable is private, because browsers can read bundled data.

## Expected result

Your design identifies vendor, required extensions, and runtime ownership without copying secrets. This is an expected result, not an execution claim.

## Common errors and troubleshooting

Missing dist: check output configuration and WORKDIR. Missing runtime libraries: match native builds to their runtime. A nested-route 404 needs web routing, not necessarily a new compilation.

## Practice exercise

**Problem and objective:** Design a Composer dependency stage and PHP runtime stage on paper.

**Success condition:** Your design identifies vendor, required extensions, and runtime ownership without copying secrets.

**Hint:** Composer is a build tool, not automatically the right application server.

## Knowledge check

1. What starts a stage?
2. Does an independent final base inherit all earlier ENV?
3. What crosses stages?
4. Can static nginx run Laravel?
5. Does multi-stage guarantee a tiny image?

> [!question]- Answers
> 1. FROM.
> 2. No.
> 3. Explicit artifacts or deliberate stage inheritance.
> 4. No.
> 5. No, copied content still matters.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/07 - Docker Layer Caching|Docker Layer Caching]]
- [[08 - Dockerfile and Compose Mastery/09 - Dockerfile Best Practices|Dockerfile Best Practices]]

## Official references

- [Multi documentation](https://docs.docker.com/build/building/multi-stage/)
- [Build documentation](https://docs.docker.com/reference/compose-file/build/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/07 - Docker Layer Caching\|Docker Layer Caching]] | [[08 - Dockerfile and Compose Mastery/09 - Dockerfile Best Practices\|Dockerfile Best Practices]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
