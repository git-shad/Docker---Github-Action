---
title: "Health Checks in CI"
subject: "GitHub Actions"
level: projects
tags:
  - github-actions
  - projects
---

# Health Checks in CI

Use a bounded readiness wait rather than a fixed sleep.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Use a bounded readiness wait rather than a fixed sleep. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I stop tests from racing database startup?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[02 - Docker Development/08 - Docker Health Checks|Docker Health Checks]], [[04 - Docker and GitHub Actions/04 - Docker Compose in CI|Docker Compose in CI]].

## Introduction, purpose, and how it works

CI startup speed varies with runner load, image pulls, and database initialization. A fixed sleep can be too short on a slow run and wasteful on a fast run. A health-based wait observes the actual dependency capability. It should fail within a known deadline so tests do not hang indefinitely. Compose --wait uses declared health checks; a service with no health check is only required to be running.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `--wait` | Wait for running or healthy services | Declare meaningful health checks for readiness. |
| `--wait-timeout SECONDS` | Limit startup waiting | Choose a measured maximum and fail on expiry. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** CI demonstration in a practice repository. The walkthrough identifies filenames and the machine where each operation belongs.

```bash
docker compose up -d --wait --wait-timeout 180 db
docker compose ps
docker compose run --rm --no-deps app php artisan migrate:status
```

## Step-by-step walkthrough

1. Run these commands as a CI shell step or in the disposable local Laravel Compose lab. They require the db healthcheck from Laravel Docker Environment.

2. The first command waits until db is healthy or the deadline expires. Do not ignore its failure with `

3.  true`.

4. The status listing provides a human-readable check. The final app command tests application credentials and schema visibility rather than merely an open TCP socket.

5. If the migration table is absent, initialize only the disposable schema using the earlier migration lesson. Readiness does not imply correct migrations or authorization.

## Expected result

Tests start after database readiness, or the wait fails within the deadline.

## Practice

Raise the database startup workload in a scratch test and observe wait behavior. Success: explain why no arbitrary fixed sleep is required.

## Common errors and troubleshooting

Unsupported --wait option: verify Compose v2 capabilities on the actual runner. Probe always fails: inspect its executable and database logs. TCP open but SQL denied: readiness is not credential validation.

## Knowledge check

1. Why avoid sleep 30?
2. What bounds waiting?
3. What if no healthcheck exists?
4. Does healthy validate app credentials?
5. Should readiness failure be ignored?

> [!question]- Answers
> 1. Startup duration varies.
> 2. --wait-timeout.
> 3. Compose only requires running state.
> 4. Not necessarily.
> 5. No.

## Cleanup

Use the isolated project’s normal CI cleanup. Preserve bounded failure logs before deleting ephemeral data.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[02 - Docker Development/08 - Docker Health Checks|Docker Health Checks]]
- [[04 - Docker and GitHub Actions/04 - Docker Compose in CI|Docker Compose in CI]]
- [[04 - Docker and GitHub Actions/06 - Laravel Automated Testing|Next in the roadmap: Laravel Automated Testing]]

## Official references

- [Health official documentation](https://docs.docker.com/compose/how-tos/startup-order/)
- [Maria official documentation](https://mariadb.com/kb/en/using-healthcheck-sh/)
- [Ci official documentation](https://docs.docker.com/build/ci/github-actions/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[04 - Docker and GitHub Actions/04 - Docker Compose in CI\|Docker Compose in CI]] | [[04 - Docker and GitHub Actions/06 - Laravel Automated Testing\|Laravel Automated Testing]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
