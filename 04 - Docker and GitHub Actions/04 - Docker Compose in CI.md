---
title: "Docker Compose in CI"
subject: "GitHub Actions"
level: projects
tags:
  - github-actions
  - projects
---

# Docker Compose in CI

Prepare dependencies, run tests, and destroy only CI-owned data.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Prepare dependencies, run tests, and destroy only CI-owned data. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I run a Laravel and MariaDB stack in CI?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[02 - Docker Development/01 - Laravel Docker Environment|Laravel Docker Environment]], [[02 - Docker Development/03 - MariaDB Container|MariaDB Container]], [[03 - GitHub Actions Fundamentals/03 - Jobs Steps and Actions|Jobs Steps and Actions]].

## Introduction, purpose, and how it works

Compose can reuse a known local service model in a disposable CI job. Give each run a unique project name so cleanup does not target an unrelated stack. The runtime app service can also run one-off Composer and Artisan commands. Here the database runs in the background while the test process runs as an ephemeral app container. No development HTTP server is needed for PHP tests.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `COMPOSE_PROJECT_NAME` | Namespace Compose resources | Set consistently in all steps. |
| `docker compose run --rm --no-deps` | Run one-off app tasks | Use after explicitly starting required dependencies. |
| `if: always()` | Attempt cleanup regardless of earlier result | Use carefully; runner loss can still prevent cleanup. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** CI demonstration in a practice repository. The walkthrough identifies filenames and the machine where each operation belongs.

```yaml
name: Learning example
on:
  workflow_dispatch:
permissions:
  contents: read
jobs:
  integration:
    runs-on: ubuntu-24.04
    timeout-minutes: 15
    env:
      COMPOSE_PROJECT_NAME: ci-${{ github.run_id }}-${{ github.run_attempt }}
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
        with:
          persist-credentials: false
      - name: Build runtime
        run: docker compose build app
      - name: Prepare application
        run: |
          cp .env.example .env
          docker compose run --rm --no-deps app composer install --no-interaction --prefer-dist
          docker compose run --rm --no-deps app php artisan key:generate
      - name: Start ready dependencies
        run: docker compose up -d --wait --wait-timeout 180 db
      - name: Test application
        run: docker compose run --rm --no-deps -e APP_ENV=testing app php artisan test
      - name: Failure logs
        if: failure()
        run: docker compose logs --no-color --tail 100
      - name: Remove disposable stack and database
        if: always()
        run: docker compose down --volumes --remove-orphans
```

## Step-by-step walkthrough

1. Use an existing Laravel 13 repository with composer.lock, .env.example, the Dockerfile, and complete compose.yaml from Laravel Docker Environment. Tests must be configured for a disposable MySQL/MariaDB database and required PHP extensions. Review phpunit.xml overrides: the default skeleton may select SQLite in-memory instead.

2. The project name is stable across steps and unique to run ID and attempt. Checkout is pinned; only read access is required.

3. Build app, copy the non-secret environment template, install locked PHP dependencies, and generate a temporary application key. Composer scripts run project code.

4. Start only db and wait for its health check. The app one-off joins the same project network and inherits the DB_HOST=db configuration.

5. Run the test suite. Tests that use RefreshDatabase may create/drop tables: confirm they target only the job-owned lesson database.

6. Failure logs precede cleanup. always() attempts cleanup after success or failure; it cannot guarantee execution after runner loss or forced termination.

## Expected result

The test exit code determines the job result, and the disposable stack is removed afterward. These notes have not executed the workflow.

## Practice

Add one intentionally failing test in a practice branch. Success: the job fails and the cleanup step still runs.

## Common errors and troubleshooting

SQLite selected unexpectedly: inspect phpunit.xml and testing environment. APP_KEY absent: verify key generation. Missing PHP extensions: extend the runtime based on composer.lock, not with ignore-platform-reqs.

## Knowledge check

1. Why unique project names?
2. Why --no-deps for the test container?
3. Does needs transfer a Compose stack across jobs?
4. What deletes the CI database?
5. Was this workflow run while writing?

> [!question]- Answers
> 1. Isolate resources between runs.
> 2. The database is already started and ready.
> 3. No.
> 4. down --volumes.
> 5. No.

## Cleanup

The last step deliberately deletes CI volumes. Never reuse this workflow’s volume deletion against staging or production projects.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[02 - Docker Development/01 - Laravel Docker Environment|Laravel Docker Environment]]
- [[02 - Docker Development/03 - MariaDB Container|MariaDB Container]]
- [[03 - GitHub Actions Fundamentals/03 - Jobs Steps and Actions|Jobs Steps and Actions]]
- [[04 - Docker and GitHub Actions/05 - Health Checks in CI|Next in the roadmap: Health Checks in CI]]

## Official references

- [Ci official documentation](https://docs.docker.com/build/ci/github-actions/)
- [Compose official documentation](https://docs.docker.com/reference/compose-file/services/)
- [Health official documentation](https://docs.docker.com/compose/how-tos/startup-order/)
- [Laravel official documentation](https://laravel.com/docs/13.x/deployment)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[04 - Docker and GitHub Actions/03 - Running Containers in CI\|Running Containers in CI]] | [[04 - Docker and GitHub Actions/05 - Health Checks in CI\|Health Checks in CI]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
