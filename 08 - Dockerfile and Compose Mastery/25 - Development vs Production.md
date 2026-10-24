---
title: "Development vs Production"
subject: Docker
level: advanced
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - advanced
---

# Development vs Production

Separate editable local services from immutable release configurations.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Separate editable local services from immutable release configurations.

## Questions answered

- How should development builds differ from production builds?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/19 - Compose Profiles and Overrides|Compose Profiles and Overrides]], [[08 - Dockerfile and Compose Mastery/20 - Compose Best Practices|Compose Best Practices]], [[08 - Dockerfile and Compose Mastery/24 - Laravel React MariaDB Example|Laravel React MariaDB Example]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

Development prioritizes visible source edits and diagnostics. Production needs a tested artifact, least privilege, stable data, explicit configuration, observability, and recovery. Artisan serve and Vite development servers are local tools. A Laravel production runtime needs an appropriate supported serving model such as PHP-FPM with a FastCGI web tier, not an nginx static copy of PHP source.

A base file with no development mounts or debug ports is safer to extend intentionally for development than trying to subtract every development feature for release. Use explicit file selections and inspect merged settings. APP_DEBUG must be false in production and APP_KEY must be stable and protected; never generate a new production key on every restart.

## Syntax reference


| Concern | Development | Production choice and reason |
| --- | --- | --- |
| Source | Bind mount for editing | Reviewed image content avoids mutable host checkout. |
| Frontend | Optional Vite HMR | Compiled assets plus deliberate public configuration. |
| Dependencies | Install dev/test tools as needed | Runtime-only dependencies after tests, with compatible requirements. |
| Data | Disposable or local named volume | Backups, restore drills and retention. |
| Secrets | Restricted local lab files | Operator-managed delivery and rotation. |
| Release | Frequent rebuild/reload | Promote tested digest and record rollback compatibility. |


## Code examples

**Base compose.yml for a static web artifact (not a complete production platform):**
```yaml
services:
  web:
    image: "${WEB_IMAGE:?Set the tested web image reference}"
    ports: ["127.0.0.1:8080:80"]
    restart: unless-stopped
```

**Development compose.dev.yml, requires the existing frontend from lesson 24:**
```yaml
services:
  web:
    build:
      context: ./frontend
    pull_policy: build
```

**Explicit inspection commands, not executed:**
```bash
docker compose -f compose.yml config --quiet
docker compose -f compose.yml -f compose.dev.yml config --quiet
```

Supply WEB_IMAGE for both resolutions, including a local tag in development. A version-control workflow should review that value.

**Incorrect release assumption:** a file named compose.prod.yml is not automatically secure or production-ready.

## Line-by-line walkthrough

1. The base requires a chosen artifact and binds only loopback.

2. The dev overlay introduces source construction intentionally.

3. The image name remains required and tags the development build; pull_policy: build makes its source choice explicit.

4. The first command inspects only the base; the second inspects its deliberate development overlay.

5. Real production additionally needs TLS/proxying, authorization, health, resource budgets, logging/alerts, host patching, backups and rollback. The small example does not supply those.

## DO ✅ / DON'T ❌

- **DO ✅** Keep environment differences explicit, because secret and network access should not accidentally cross boundaries.
- **DO ✅** Promote a tested digest where feasible, because rebuilding after approval changes the artifact under review.

- **DON'T ❌** Use a development source mount or debug mode in release without a specific reviewed need, because it changes exposure and reproducibility.
- **DON'T ❌** Assume deploy settings are identical across backends, because orchestration support varies.

## Expected result

Every difference has a reason, owner and verification method; no release credential is required for frontend compilation. This is an expected result, not an execution claim.

## Common errors and troubleshooting

A development override can silently add ports or mounts if loaded automatically. A frontend build-time URL may require runtime public configuration to support build-once promotion. A PHP-FPM/HTTP protocol mismatch causes proxy failure even when processes run.

## Practice exercise

**Problem and objective:** Write a development/release configuration inventory for the stack in lesson 24.

**Success condition:** Every difference has a reason, owner and verification method; no release credential is required for frontend compilation.

**Hint:** Distinguish image content, runtime settings and external infrastructure.

## Knowledge check

1. Is artisan serve a production model here?
2. Should a production APP_KEY rotate each restart?
3. Does a prod filename prove security?
4. Why promote a digest?
5. What remains beyond Compose?

> [!question]- Answers
> 1. No.
> 2. No.
> 3. No.
> 4. To deploy the exact tested bytes.
> 5. Infrastructure, monitoring, recovery and operational policy.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/19 - Compose Profiles and Overrides|Compose Profiles and Overrides]]
- [[08 - Dockerfile and Compose Mastery/20 - Compose Best Practices|Compose Best Practices]]
- [[08 - Dockerfile and Compose Mastery/24 - Laravel React MariaDB Example|Laravel React MariaDB Example]]
- [[08 - Dockerfile and Compose Mastery/26 - Debugging Docker Configurations|Debugging Docker Configurations]]

## Official references

- [Best documentation](https://docs.docker.com/build/building/best-practices/)
- [Deploy documentation](https://docs.docker.com/reference/compose-file/deploy/)
- [Merge documentation](https://docs.docker.com/reference/compose-file/merge/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/24 - Laravel React MariaDB Example\|Laravel React MariaDB Example]] | [[08 - Dockerfile and Compose Mastery/26 - Debugging Docker Configurations\|Debugging Docker Configurations]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
