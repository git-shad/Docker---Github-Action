---
title: "Bind Mounts and Hot Reload"
subject: "Docker"
level: intermediate
tags:
  - docker
  - intermediate
---

# Bind Mounts and Hot Reload

Edit a host file and observe a container read the new content.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Edit a host file and observe a container read the new content. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do bind mounts and hot reload work together?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). PHP examples use PHP 8.4; frontend examples use Node 22; database examples use MariaDB 11.4. Check the project’s own support constraints. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[02 - Docker Development/05 - Environment Variables|Environment Variables]].

## Introduction, purpose, and how it works

COPY embeds a snapshot at image-build time. A bind mount exposes a live host path at runtime. Hot reload is a separate application feature: a watcher must notice edits and rebuild or restart. Mounting code alone does not enable Vite hot module replacement. A mount over /app can hide dependencies previously installed into that path in the image.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `--mount type=bind,src=PATH,dst=PATH` | Expose a host path | Use for editable code; a remote daemon uses its own host paths. |
| `readonly` | Prevent container writes to a mount | Use for inspection or static config; watchers may need separate writable caches. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```bash
mkdir -p lesson-source
printf "first\n" > lesson-source/message.txt
docker run --rm --mount type=bind,src="$PWD/lesson-source",dst=/source,readonly alpine:3.22 cat /source/message.txt
printf "second\n" > lesson-source/message.txt
docker run --rm --mount type=bind,src="$PWD/lesson-source",dst=/source,readonly alpine:3.22 cat /source/message.txt
```

## Step-by-step walkthrough

1. Run in a local Ubuntu Bash lab directory. The commands create or overwrite only lesson-source/message.txt; do not reuse a directory containing valuable work.

2. The absolute source path points at the host directory; /source is the container destination. readonly protects host files from writes through this mount.

3. The first reader prints the initial content. The host edit changes the same mounted file before the second reader starts.

4. For an existing React/Vite project, use a Node development server listening on 0.0.0.0 and publish its port only on loopback. Configure the HMR websocket address for your actual browser route; do not expose the dev server to the internet.

## Expected result

The readers print first and second without an image rebuild.

## Practice

Explain how the same edit behaves with COPY instead. Success: identify when rebuilding is required and when a watcher is required.

## Common errors and troubleshooting

Mount path missing: create it first when using --mount. Missing node_modules after mounting /app: the host mount hid image files; install dependencies into the chosen mounted workspace. Docker Desktop file sharing and WSL paths differ from native Linux.

## Knowledge check

1. Does COPY watch files?
2. Does a bind mount enable HMR by itself?
3. What does readonly protect?
4. Can a mount hide image files?
5. Where is the source path resolved?

> [!question]- Answers
> 1. No.
> 2. No.
> 3. Writes through that mount.
> 4. Yes.
> 5. On the Docker daemon host.

## Cleanup

No container remains. Keep or manually remove only the lab message file you created; no host-wide deletion commands are needed.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[02 - Docker Development/05 - Environment Variables|Environment Variables]]
- [[02 - Docker Development/07 - Multi-stage Docker Builds|Next in the roadmap: Multi-stage Docker Builds]]

## Official references

- [Mount official documentation](https://docs.docker.com/engine/storage/bind-mounts/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[02 - Docker Development/05 - Environment Variables\|Environment Variables]] | [[02 - Docker Development/07 - Multi-stage Docker Builds\|Multi-stage Docker Builds]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
