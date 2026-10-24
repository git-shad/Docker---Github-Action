---
title: "Dockerfile Examples"
subject: "GitHub Actions"
level: projects
tags:
  - github-actions
  - projects
---

# Dockerfile Examples

Compare two small runtime models instead of copying one image for every application.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Compare two small runtime models instead of copying one image for every application. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I choose a Dockerfile for a PHP command or a static frontend?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[01 - Docker Fundamentals/03 - Dockerfile Instructions|Dockerfile Instructions]], [[02 - Docker Development/07 - Multi-stage Docker Builds|Multi-stage Docker Builds]].

## Introduction, purpose, and how it works

A PHP command image executes PHP directly. A static frontend image serves compiled browser files over HTTP. Laravel needs framework code, Composer dependencies, required extensions, runtime configuration, and usually a production process model such as PHP-FPM behind an HTTP server. React output does not require Node at runtime when it is static. Choose the process first, then the image, not the reverse.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `USER UID:GID` | Set the runtime process identity | Use after preparing files and writable paths. |
| `CMD ["php", "/app/hello.php"]` | Execute a PHP command | Use exec form for direct process startup. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```dockerfile
FROM php:8.4-cli
WORKDIR /app
COPY hello.php /app/hello.php
USER 10001:10001
CMD ["php", "/app/hello.php"]
```

## Companion file: hello.php

```php
<?php
echo "hello from PHP\n";
```

## Step-by-step walkthrough

1. In a new disposable directory create hello.php containing the PHP block below and save this Dockerfile. This is a PHP command exercise, not a Laravel web server.

2. FROM supplies PHP; WORKDIR selects /app; COPY embeds the script. USER switches away from root because the script only needs read access. It cannot write arbitrary image directories unless permissions allow it.

3. Build with `docker build -t lesson-php .` and run `docker run --rm lesson-php`.

4. For browser assets use the complete multi-stage Dockerfile in Multi-stage Docker Builds instead. Compare its final nginx process with this final PHP process; do not run PHP source as static web assets.

## Expected result

The command prints hello from PHP as a non-root process.

## Practice

Add an echo of the effective UID using a supported PHP extension or inspect with `docker run --rm lesson-php id`. Success: confirm the configured non-root UID without granting privileges.

## Common errors and troubleshooting

PHP script not found: verify COPY context. Permission denied after USER: grant only the specific writable directories required. Never expose an application repository, .env, or PHP source directory as a static document root.

## Knowledge check

1. What runs here?
2. Does this serve HTTP?
3. Why USER?
4. What serves built React files?
5. Can nginx static hosting execute Laravel?

> [!question]- Answers
> 1. The PHP CLI.
> 2. No.
> 3. Reduce process privileges.
> 4. An HTTP server such as nginx.
> 5. No.

## Cleanup

The container removes itself. Remove the local lesson-php image if no longer needed; keep actual application images intact.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[01 - Docker Fundamentals/03 - Dockerfile Instructions|Dockerfile Instructions]]
- [[02 - Docker Development/07 - Multi-stage Docker Builds|Multi-stage Docker Builds]]
- [[06 - Practical Examples/02 - Docker Compose Examples|Next in the roadmap: Docker Compose Examples]]

## Official references

- [File official documentation](https://docs.docker.com/reference/dockerfile/)
- [Multi official documentation](https://docs.docker.com/build/building/multi-stage/)
- [Laravel official documentation](https://laravel.com/docs/13.x/deployment)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[05 - Advanced CI CD/10 - Complete CI CD Pipeline\|Complete CI CD Pipeline]] | [[06 - Practical Examples/02 - Docker Compose Examples\|Docker Compose Examples]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
