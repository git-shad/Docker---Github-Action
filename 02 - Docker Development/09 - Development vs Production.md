---
title: "Development vs Production"
subject: "Docker"
level: intermediate
tags:
  - docker
  - intermediate
---

# Development vs Production

Assess release readiness using explicit operational requirements.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Assess release readiness using explicit operational requirements. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- What must change before a development container can serve production?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). PHP examples use PHP 8.4; frontend examples use Node 22; database examples use MariaDB 11.4. Check the project’s own support constraints. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[02 - Docker Development/08 - Docker Health Checks|Docker Health Checks]].

## Introduction, purpose, and how it works

Development favors rapid edits and diagnostics; production favors stable artifacts, least privilege, observability, and recoverable data. A production image should contain reviewed code and dependencies rather than relying on an arbitrary host checkout. PHP-FPM speaks FastCGI, not HTTP, and needs a compatible web server. Artisan serve and Vite dev servers are for development. A single-host Compose release still requires patching, TLS, backups, monitoring, and a rollback strategy.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `image: ${WEB_IMAGE:?message}` | Require a configured artifact | Use to reject missing release configuration. |
| `restart: unless-stopped` | Restart exited services | Use with monitoring; it does not fix a bad release. |
| `mem_limit: 256m` | Bound container memory | Use a measured budget; undersizing can trigger OOM kills. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```yaml
services:
  web:
    image: "${WEB_IMAGE:?Set a tested web image reference}"
    ports:
      - "127.0.0.1:8080:80"
    restart: unless-stopped
    mem_limit: 256m
    cpus: "1.0"
```

## Step-by-step walkthrough

1. This is a production-planning exercise, not a deployable Laravel architecture. Save it as a separate compose.yaml in a scratch directory if validating syntax. WEB_IMAGE must be an HTTP image listening on port 80, such as the frontend image from Multi-stage Docker Builds.

2. Set WEB_IMAGE to your tested image reference in the shell and run `docker compose config --quiet` only if you choose to validate locally. The required-value expression rejects an unset value.

3. restart configures process-exit behavior. Memory and CPU limits constrain this service; size them from measurements rather than copying the lab numbers.

4. Before deployment, document TLS termination, health probes, logging retention, service identity, writable directories, secret delivery, alerting, and tested restore procedures. For Laravel set APP_DEBUG=false, preserve APP_KEY, and separate queue workers and scheduling responsibilities.

## Expected result

A written readiness review identifies missing requirements. This small snippet does not claim to be production-ready.

## Practice

Create a two-column dev/production checklist for your application. Success: every production requirement has an owner and verification method.

## Common errors and troubleshooting

502 through a proxy: verify the expected HTTP versus FastCGI protocol. Restart loops: inspect exit codes and resource limits. An image built successfully can still fail because runtime config or persistent state differs.

## Knowledge check

1. Is php-fpm an HTTP server?
2. Should production mount arbitrary source?
3. Does restart replace monitoring?
4. Can an image back up a database volume?
5. What must rollback consider besides code?

> [!question]- Answers
> 1. No.
> 2. Prefer immutable reviewed artifacts.
> 3. No.
> 4. No.
> 5. Database schema and persistent state.

## Cleanup

No resources need to be started for this planning exercise. Preserve the review alongside your own deployment documentation.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[02 - Docker Development/08 - Docker Health Checks|Docker Health Checks]]
- [[03 - GitHub Actions Fundamentals/01 - Introduction to CI CD|Next in the roadmap: Introduction to CI CD]]

## Official references

- [Compose official documentation](https://docs.docker.com/reference/compose-file/services/)
- [Laravel official documentation](https://laravel.com/docs/13.x/deployment)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[02 - Docker Development/08 - Docker Health Checks\|Docker Health Checks]] | [[03 - GitHub Actions Fundamentals/01 - Introduction to CI CD\|Introduction to CI CD]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
