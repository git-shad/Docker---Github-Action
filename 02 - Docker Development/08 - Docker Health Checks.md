---
title: "Docker Health Checks"
subject: "Docker"
level: intermediate
tags:
  - docker
  - intermediate
---

# Docker Health Checks

Probe database readiness and inspect probe results.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Probe database readiness and inspect probe results. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How does Docker distinguish running from healthy?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). PHP examples use PHP 8.4; frontend examples use Node 22; database examples use MariaDB 11.4. Check the project’s own support constraints. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[02 - Docker Development/07 - Multi-stage Docker Builds|Multi-stage Docker Builds]].

## Introduction, purpose, and how it works

A running state means the main process exists. Health is a separate status updated by a probe’s exit code. MariaDB can be running while initializing storage. A useful probe checks the capability its consumers need, with bounded timeouts and retries. Docker Engine does not automatically restart a container merely because its health becomes unhealthy.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `healthcheck.test` | Define the readiness command | Use an executable available in the image. |
| `start_period: 30s` | Allow initialization grace | Use for slow starts; it is not an unconditional sleep. |
| `--wait-timeout 180` | Bound Compose readiness waiting | Use in labs and CI; failure should stop downstream steps. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```yaml
services:
  db:
    image: mariadb:11.4
    environment:
      MARIADB_ROOT_PASSWORD: local-only-root-password
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      interval: 5s
      timeout: 3s
      retries: 20
      start_period: 30s
```

## Step-by-step walkthrough

1. Use a fresh disposable directory and save compose.yaml. The public password is only for this non-published local database.

2. CMD form invokes the image-provided helper directly. --connect tests connectivity; --innodb_initialized checks storage-engine initialization.

3. interval schedules probes; timeout bounds a probe; retries counts consecutive failures; start_period tolerates initial startup failures.

4. Run `docker compose up -d --wait --wait-timeout 180`, then `docker compose ps` and `docker inspect --format '{{json .State.Health}}' "$(docker compose ps -q db)"` from Bash.

## Expected result

The database becomes healthy after initialization; the health metadata contains recent exit codes and probe output.

## Practice

Use an intentionally invalid probe option in the disposable lab, recreate the service, and inspect health logs. Success: distinguish a broken probe from a broken database.

## Common errors and troubleshooting

Command not found: the image lacks the selected probe executable. Endless starting: inspect startup timing and database logs. A service_healthy dependency governs startup ordering, not continuous recovery.

## Knowledge check

1. Is running the same as ready?
2. What reports probe success?
3. What does timeout bound?
4. Does unhealthy automatically restart in Engine?
5. What handles startup grace?

> [!question]- Answers
> 1. No.
> 2. Exit code zero.
> 3. One probe execution.
> 4. No.
> 5. start_period.

## Cleanup

`docker compose down --volumes` removes this disposable database and any attached anonymous volumes, destroying lab data. Do not apply it to a real stack.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[02 - Docker Development/07 - Multi-stage Docker Builds|Multi-stage Docker Builds]]
- [[02 - Docker Development/09 - Development vs Production|Next in the roadmap: Development vs Production]]

## Official references

- [Health official documentation](https://docs.docker.com/compose/how-tos/startup-order/)
- [Maria official documentation](https://mariadb.com/kb/en/using-healthcheck-sh/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[02 - Docker Development/07 - Multi-stage Docker Builds\|Multi-stage Docker Builds]] | [[02 - Docker Development/09 - Development vs Production\|Development vs Production]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
