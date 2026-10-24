---
title: "Multi-stage Docker Builds"
subject: "Docker"
level: intermediate
tags:
  - docker
  - intermediate
---

# Multi-stage Docker Builds

Compile a React frontend and copy only its static output into a web-server image.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Compile a React frontend and copy only its static output into a web-server image. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do multi-stage builds keep Node.js out of the final web image?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). PHP examples use PHP 8.4; frontend examples use Node 22; database examples use MariaDB 11.4. Check the project’s own support constraints. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[02 - Docker Development/06 - Bind Mounts and Hot Reload|Bind Mounts and Hot Reload]].

## Introduction, purpose, and how it works

A build stage is a named portion of a Dockerfile beginning at FROM. Stages can use different tools. A final image contains its own base plus explicitly copied artifacts, not every earlier toolchain. For React with TypeScript and Vite, Node compiles assets and nginx serves the resulting dist directory. This does not create a PHP runtime or an API.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `FROM IMAGE AS NAME` | Name a build stage | Use to separate compilers from runtime. |
| `COPY --from=assets SOURCE DEST` | Copy stage output | Use for final artifacts; paths refer to that stage filesystem. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```dockerfile
FROM node:22-bookworm-slim AS assets
WORKDIR /src
COPY package.json package-lock.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:stable-alpine
COPY --from=assets /src/dist /usr/share/nginx/html
```

## Step-by-step walkthrough

1. Use an existing Vite React TypeScript project with a package-lock.json and build script that writes dist. Save the block as Dockerfile. Add node_modules, dist, .git, and .env files to .dockerignore.

2. The first stage copies manifests before source so dependency installation can be cached across source-only edits. npm ci enforces the lock file.

3. The second COPY includes source; the build command compiles it. The final FROM starts a separate filesystem without Node.

4. Build with `docker build -t lesson-react .`, then run `docker run -d --name lesson-react-web -p 127.0.0.1:8080:80 lesson-react`.

5. Visit the local URL. Client-side routes may require an nginx fallback to index.html; this minimal example covers the root page only. Vite frontend variables are public build-time values, never secrets.

## Expected result

The compiled root page loads from nginx, and Node is absent from the final runtime.

## Practice

Compare `docker image inspect lesson-react` with the Node base size and inspect image history. Success: explain why build tools need not ship to users.

## Common errors and troubleshooting

COPY dist not found: check the build output folder. TypeScript errors should fail the build, not be ignored. Refreshing an SPA nested URL may return 404 until routing fallback is configured.

## Knowledge check

1. What starts a stage?
2. What names it?
3. What crosses stages?
4. Is a frontend env value secret?
5. Why copy lock files first?

> [!question]- Answers
> 1. FROM.
> 2. AS assets.
> 3. Only explicitly copied files.
> 4. No, it can be bundled publicly.
> 5. To reuse the dependency layer across source edits.

## Cleanup

`docker stop lesson-react-web` then `docker rm lesson-react-web` removes the server. `docker image rm lesson-react` removes the local lesson image.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[02 - Docker Development/06 - Bind Mounts and Hot Reload|Bind Mounts and Hot Reload]]
- [[02 - Docker Development/08 - Docker Health Checks|Next in the roadmap: Docker Health Checks]]

## Official references

- [Multi official documentation](https://docs.docker.com/build/building/multi-stage/)
- [File official documentation](https://docs.docker.com/reference/dockerfile/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[02 - Docker Development/06 - Bind Mounts and Hot Reload\|Bind Mounts and Hot Reload]] | [[02 - Docker Development/08 - Docker Health Checks\|Docker Health Checks]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
