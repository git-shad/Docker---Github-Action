---
title: "PHP Composer and Node.js"
subject: "Docker"
level: intermediate
tags:
  - docker
  - intermediate
---

# PHP Composer and Node.js

Separate PHP dependency installation from frontend asset compilation.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Separate PHP dependency installation from frontend asset compilation. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- Why do Laravel projects need Composer and sometimes Node.js?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). PHP examples use PHP 8.4; frontend examples use Node 22; database examples use MariaDB 11.4. Check the project’s own support constraints. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[02 - Docker Development/01 - Laravel Docker Environment|Laravel Docker Environment]].

## Introduction, purpose, and how it works

Composer resolves PHP packages using composer.json and composer.lock. npm resolves JavaScript dependencies using package.json and package-lock.json. Laravel can use Vite to compile browser assets; PHP does not execute TypeScript or bundle React. Lock files record concrete dependency versions. Use install commands that respect them instead of updating dependencies during every build.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `composer install` | Install locked PHP dependencies | Use for repeatable installs; composer update intentionally changes resolution. |
| `npm ci` | Install the npm lock file exactly | Use in CI; it requires package-lock.json. |
| `npm run build` | Execute the build script | Use for assets; the script must exist. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```bash
docker compose run --rm --no-deps app composer install --no-interaction
docker run --rm --user "$(id -u):$(id -g)" -e HOME=/tmp --mount type=bind,src="$PWD",dst=/app -w /app node:22-bookworm-slim npm ci
docker run --rm --user "$(id -u):$(id -g)" -e HOME=/tmp --mount type=bind,src="$PWD",dst=/app -w /app node:22-bookworm-slim npm run build
```

## Step-by-step walkthrough

1. Use the Laravel Docker Environment project in Ubuntu Bash. It must contain committed Composer and npm lock files and a valid build script. Node 22 is a teaching baseline; verify your package engines and framework support.

2. The Compose command uses the PHP runtime to install vendor dependencies. Application install scripts may execute code, so inspect dependencies and use a trusted checkout.

3. The Node containers mount the project, select `/app`, and run with your host numeric user/group to reduce root-owned output. HOME=/tmp provides a writable cache location.

4. npm ci refuses incompatible lock files and replaces node_modules. Build runs the project’s package.json build script; for typical Laravel Vite projects it writes public/build.

## Expected result

Both vendor and compiled frontend assets exist. A successful frontend build does not prove PHP tests passed.

## Practice

Inspect both lock files and identify one package in each. Success: explain which runtime consumes each package.

## Common errors and troubleshooting

npm ci without a lock file fails: generate and review a lock file in a separate dependency-maintenance task. PHP extension errors require runtime changes. UID behavior here assumes Linux Bash; do not paste unchanged into PowerShell.

## Knowledge check

1. What installs PHP packages?
2. What installs browser build dependencies?
3. Why npm ci?
4. Does npm build test PHP?
5. Why select a numeric user?

> [!question]- Answers
> 1. Composer.
> 2. npm.
> 3. It enforces the committed lock file.
> 4. No.
> 5. To reduce root-owned files on a Linux bind mount.

## Cleanup

One-off containers remove themselves. Keep generated artifacts only in your disposable checkout; do not delete lock files to “fix” reproducibility.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[02 - Docker Development/01 - Laravel Docker Environment|Laravel Docker Environment]]
- [[02 - Docker Development/03 - MariaDB Container|Next in the roadmap: MariaDB Container]]

## Official references

- [Laravel official documentation](https://laravel.com/docs/13.x/deployment)
- [File official documentation](https://docs.docker.com/reference/dockerfile/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[02 - Docker Development/01 - Laravel Docker Environment\|Laravel Docker Environment]] | [[02 - Docker Development/03 - MariaDB Container\|MariaDB Container]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
