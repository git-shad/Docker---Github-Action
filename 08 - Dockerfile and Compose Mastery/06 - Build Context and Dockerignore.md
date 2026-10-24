---
title: "Build Context and Dockerignore"
subject: Docker
level: intermediate
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - intermediate
---

# Build Context and Dockerignore

Control context inputs before they reach a builder.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Control context inputs before they reach a builder.

## Questions answered

- Why can COPY fail when a file exists near my Dockerfile?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/04 - COPY ADD WORKDIR and USER|COPY ADD WORKDIR and USER]], [[08 - Dockerfile and Compose Mastery/05 - ARG ENV EXPOSE and VOLUME|ARG ENV EXPOSE and VOLUME]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

Build context is the input tree supplied to the builder, while Dockerfile location is a separate selection. COPY sources are relative to context. A root .dockerignore filters it; Dockerfile-specific files such as Dockerfile.dev.dockerignore can take precedence for a selected recipe. A .gitignore does not filter build inputs.

Patterns support wildcards, ** and ! exceptions. The last matching rule controls inclusion. A broader context can solve missing sibling inputs but also includes more material, so choose the boundary deliberately.

## Syntax reference

CLI: docker build -f RECIPE CONTEXT. Compose build.context chooses input root; build.dockerfile chooses the recipe relative to that context.

## Code examples

**.dockerignore example; adapt to required build inputs:**
```text
.git
.env
.env.*
!.env.example
**/node_modules
**/vendor
**/dist
**/storage/logs/*
secrets
backups
```

**Existing-layout command from repository root:**
```bash
docker build -f backend/Dockerfile -t mastery-backend ./backend
```

Requires backend/Dockerfile and its inputs. With that context the following is incorrect:
```dockerfile
COPY ../frontend /frontend
```

Choose a context that includes needed source and update COPY paths; do not include private siblings by accident.

## Line-by-line walkthrough

1. The .git rule filters repository internals; it does not rewrite history.

2. Environment patterns exclude private local configuration; the later exception allows only a non-secret template.

3. Nested dependency and output rules avoid platform-specific host content or stale builds.

4. The command selects backend as context, independent of the recipe flag.

5. The sibling frontend is outside that boundary, so ordinary COPY cannot read it.

## DO ✅ / DON'T ❌

- **DO ✅** Inspect all necessary inputs before choosing context, because missing and overbroad inputs are both errors.
- **DO ✅** Keep allowed templates truly non-sensitive, because a filename does not guarantee safe content.

- **DON'T ❌** Assume .dockerignore erases a secret from Git or existing images, because it only filters future context.
- **DON'T ❌** Copy host node_modules as runtime truth, because native dependencies may target a different platform.

## Expected result

Required manifests remain available and local secrets/dependencies are excluded. This is an expected result, not an execution claim.

## Common errors and troubleshooting

Checksum/copy failures can indicate missing or ignored inputs. Check context, ignore ordering, path case, and generated files. Do not treat every such error as an internal hashing failure.

## Practice exercise

**Problem and objective:** Write ignore rules that exclude all private .env variants but retain a public example template.

**Success condition:** Required manifests remain available and local secrets/dependencies are excluded.

**Hint:** Place the explicit exception after the broader exclusion.

## Knowledge check

1. Is context necessarily recipe directory?
2. Does .gitignore filter context?
3. What does ! do?
4. Can COPY escape context?
5. Why avoid host dependencies?

> [!question]- Answers
> 1. No.
> 2. No.
> 3. Re-include matching paths according to rule order.
> 4. No.
> 5. Platform and reproducibility differences.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/04 - COPY ADD WORKDIR and USER|COPY ADD WORKDIR and USER]]
- [[08 - Dockerfile and Compose Mastery/05 - ARG ENV EXPOSE and VOLUME|ARG ENV EXPOSE and VOLUME]]
- [[08 - Dockerfile and Compose Mastery/07 - Docker Layer Caching|Docker Layer Caching]]

## Official references

- [Context documentation](https://docs.docker.com/build/concepts/context/)
- [Build documentation](https://docs.docker.com/reference/compose-file/build/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/05 - ARG ENV EXPOSE and VOLUME\|ARG ENV EXPOSE and VOLUME]] | [[08 - Dockerfile and Compose Mastery/07 - Docker Layer Caching\|Docker Layer Caching]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
