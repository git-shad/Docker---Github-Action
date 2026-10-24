---
title: "Laravel Automated Testing"
subject: "GitHub Actions"
level: projects
tags:
  - github-actions
  - projects
---

# Laravel Automated Testing

Run PHP tests against explicitly disposable configuration.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Run PHP tests against explicitly disposable configuration. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I keep Laravel tests isolated from real data?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[04 - Docker and GitHub Actions/04 - Docker Compose in CI|Docker Compose in CI]].

## Introduction, purpose, and how it works

Laravel test configuration can override normal runtime configuration through phpunit.xml and .env.testing. Database-reset traits may drop tables and rebuild schema. Therefore a test environment must never contain production connection strings. Unit tests isolate small behavior; feature tests exercise framework requests and may require a database. APP_ENV=testing is a useful marker, but the actual database host and credentials determine the risk.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `php artisan test --testsuite=NAME` | Run a named test suite | Use actual phpunit.xml names. |
| `APP_ENV=testing` | Label the test environment | Use alongside explicitly disposable DB settings. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** CI demonstration in a practice repository. The walkthrough identifies filenames and the machine where each operation belongs.

```bash
docker compose run --rm --no-deps -e APP_ENV=testing app php artisan config:clear
docker compose run --rm --no-deps -e APP_ENV=testing app php artisan test --testsuite=Unit
docker compose run --rm --no-deps -e APP_ENV=testing app php artisan test --testsuite=Feature
```

## Step-by-step walkthrough

1. Use the prepared disposable Laravel CI stack from Docker Compose in CI; the database must already be healthy, Composer dependencies installed, and APP_KEY configured.

2. Inspect phpunit.xml and .env.testing. For this lab select mysql with db:3306, lesson database and its local-only account; remove conflicting SQLite overrides if intentionally testing MariaDB. Do not copy production .env.

3. Clear local cached config so stale settings do not redirect connections. Run the Unit suite first, then Feature. The project must define these suite names; adapt names to its actual phpunit.xml.

4. Use the test framework’s disposable migration/reset lifecycle for database tests. Capture failure details without printing passwords or customer records.

## Expected result

Each suite returns a reliable success or failure status. Database integration tests actually use the intended disposable engine.

## Practice

Add a simple failing assertion in a sandbox test, then fix it. Success: the job fails and recovers because of test behavior, not ignored exit codes.

## Common errors and troubleshooting

No tests executed: check suite names and discovery paths. Missing extension: update the test runtime. Table not found: review migration/reset setup. SQLite-only success does not prove MariaDB-specific SQL works.

## Knowledge check

1. What can RefreshDatabase affect?
2. Is APP_ENV alone enough isolation?
3. What should be installed?
4. Does Unit imply database coverage?
5. Why inspect phpunit.xml?

> [!question]- Answers
> 1. The configured test schema.
> 2. No, verify connection settings.
> 3. Locked dependencies including test packages.
> 4. No.
> 5. It can override environment and database selection.

## Cleanup

Remove only the CI-owned stack and volumes using its final cleanup step. Never run test reset commands against a shared database.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[04 - Docker and GitHub Actions/04 - Docker Compose in CI|Docker Compose in CI]]
- [[04 - Docker and GitHub Actions/07 - React TypeScript Build Validation|Next in the roadmap: React TypeScript Build Validation]]

## Official references

- [Laravel official documentation](https://laravel.com/docs/13.x/deployment)
- [Ci official documentation](https://docs.docker.com/build/ci/github-actions/)

- [Laravel testing documentation](https://laravel.com/docs/13.x/testing)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[04 - Docker and GitHub Actions/05 - Health Checks in CI\|Health Checks in CI]] | [[04 - Docker and GitHub Actions/07 - React TypeScript Build Validation\|React TypeScript Build Validation]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
