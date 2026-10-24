---
title: "Compose Depends On and Healthchecks"
subject: Docker
level: intermediate
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - intermediate
---

# Compose Depends On and Healthchecks

Design bounded readiness checks and dependency sequencing.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Design bounded readiness checks and dependency sequencing.

## Questions answered

- What do depends_on and healthcheck actually guarantee?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/16 - Compose Environment Variables|Compose Environment Variables]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

depends_on short form starts dependencies before dependents but does not prove application readiness. Long form service_healthy waits for the dependency’s health state at initial startup. service_completed_successfully fits one-shot initialization jobs. healthcheck defines the observable capability and retry timing. An application still needs reconnect/error handling after startup.

A health transition does not automatically restart a container in Docker Engine. Service restart policy reacts to process lifecycle. depends_on restart: true concerns explicit Compose-controlled dependency updates/restarts, not every runtime failure. Probe tools must exist and must not leak credentials into logs.

## Syntax reference


| Setting | What it does | Scenario |
| --- | --- | --- |
| depends_on | Order or gate startup | Delay app creation until db is healthy. |
| healthcheck | Measure a service capability | Use MariaDB-provided connectivity/initialization helper. |
| restart | Handle process exits per policy | Restart a crashed service; not an unhealthy-only trigger. |


## Code examples

**Complete disposable database readiness model; supply a local password through the required host variable:**
```yaml
services:
  db:
    image: mariadb:11.4
    environment:
      MARIADB_ROOT_PASSWORD: "${LAB_DB_ROOT_PASSWORD:?Supply a disposable lab password}"
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      interval: 5s
      timeout: 3s
      retries: 20
      start_period: 30s
  observer:
    image: alpine:3.22
    depends_on:
      db:
        condition: service_healthy
    command: ["printf", "Database readiness gate passed\n"]
```

The database is not published on the host. The observer demonstrates ordering only; it does not authenticate as an application.

**Incorrect readiness assumption fragment:**
```yaml
depends_on: [db]
```

This is valid syntax but not equivalent to waiting for health.

## Line-by-line walkthrough

1. The required variable prevents accidental empty root credentials; use only a disposable local value.

2. The helper is provided by the MariaDB image and tests connection plus InnoDB initialization.

3. Timing bounds each check and tolerates initialization.

4. observer waits for the declared healthy condition.

5. A later local startup could use docker compose up -d --wait --wait-timeout 180, but a one-shot observer exits: target db when waiting for long-running readiness, then run observer separately if needed.

## DO ✅ / DON'T ❌

- **DO ✅** Test the capability consumers need, because a running PID can still be initializing.
- **DO ✅** Bound startup waiting, because CI must fail rather than hang indefinitely.

- **DON'T ❌** Add arbitrary sleep as the only readiness strategy, because machine speeds vary.
- **DON'T ❌** Assume startup ordering continuously supervises dependency health, because later failures remain application concerns.

## Expected result

Your explanation separates startup ordering, health state, and restart behavior. This is an expected result, not an execution claim.

## Common errors and troubleshooting

Probe command missing: inspect image tooling. Check healthy but access denied: app credentials/schema were not proved by infrastructure readiness. Unsupported timing flags: check Engine/Compose versions.

## Practice exercise

**Problem and objective:** Add an app dependency on db health without claiming ongoing recovery.

**Success condition:** Your explanation separates startup ordering, health state, and restart behavior.

**Hint:** Use service_healthy and application retry handling for different purposes.

## Knowledge check

1. Does short depends_on prove readiness?
2. What does health exit 0 mean?
3. Does unhealthy alone restart Engine containers?
4. What bounds one probe?
5. Do consumers still need reconnect logic?

> [!question]- Answers
> 1. No.
> 2. Probe success.
> 3. No.
> 4. timeout.
> 5. Yes.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/16 - Compose Environment Variables|Compose Environment Variables]]
- [[08 - Dockerfile and Compose Mastery/18 - Compose Commands and Entrypoints|Compose Commands and Entrypoints]]

## Official references

- [Health documentation](https://docs.docker.com/compose/how-tos/startup-order/)
- [Services documentation](https://docs.docker.com/reference/compose-file/services/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/16 - Compose Environment Variables\|Compose Environment Variables]] | [[08 - Dockerfile and Compose Mastery/18 - Compose Commands and Entrypoints\|Compose Commands and Entrypoints]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
