---
title: "Dockerfile Best Practices"
subject: Docker
level: advanced
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - advanced
---

# Dockerfile Best Practices

Review reproducibility, permissions, inputs, and operational behavior.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Review reproducibility, permissions, inputs, and operational behavior.

## Questions answered

- What makes a Dockerfile reliable rather than merely short?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/08 - Multi-stage Builds|Multi-stage Builds]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

Choose maintained bases appropriate to the application and record real digests for exact release identity. A digest also freezes old vulnerabilities until intentionally updated, so pinning requires a patch process. Keep dependency locks, separate toolchains where useful, and avoid unnecessary OS packages. Group related installation/cleanup operations without collapsing unrelated logic into unreadable chains.

A runtime must have explicit identity, writable paths, startup behavior, shutdown, and logs. Minimal images still need an operational diagnostic plan. Build success does not prove application readiness or correct architecture.

## Syntax reference

Review: base trust/version/digest, context exclusions, dependency locks, cache boundaries, UID/GID, required writes, startup argv, health, stop signal, labels, patch cadence.

## Code examples

**Complete PHP CLI image; companion hello.php follows:**
```dockerfile
FROM php:8.4-cli
WORKDIR /app
COPY --chown=10001:10001 hello.php ./hello.php
USER 10001:10001
CMD ["php", "/app/hello.php"]
```

```php
<?php
echo "reviewable PHP runtime\n";
```

**Poorly controlled fragment:**
```dockerfile
FROM php:latest
COPY . /app
```

Choose an explicit supported base and explicit inputs, with reviewed ignore rules.

## Line-by-line walkthrough

1. The base states PHP version and CLI role; project compatibility remains a prerequisite.

2. WORKDIR makes paths predictable.

3. COPY includes a single readable script, not arbitrary host files.

4. USER lowers runtime privilege; this process needs no writable application directory.

5. CMD runs PHP directly and writes logs/output to standard streams.

## DO ✅ / DON'T ❌

- **DO ✅** Document why each package and writable path exists, because unnecessary surface accumulates.
- **DO ✅** Schedule updates to pinned artifacts, because frozen old content is not automatically safe.

- **DON'T ❌** Use ignore-platform-reqs as a normal fix, because missing extensions can remain until runtime.
- **DON'T ❌** Judge security only by image size, because privileges and secrets matter too.

## Expected result

Each improvement names a real failure mode and a verification method. This is an expected result, not an execution claim.

## Common errors and troubleshooting

Permission failures need path-specific ownership changes. Extension errors require the project’s actual Composer constraints. Architecture failures require platform-compatible inputs, not arbitrary permission elevation.

## Practice exercise

**Problem and objective:** Review an existing recipe and propose three justified improvements.

**Success condition:** Each improvement names a real failure mode and a verification method.

**Hint:** Prioritize correctness and privilege before cosmetic line counts.

## Knowledge check

1. Does a digest update itself?
2. Is smallest always safest?
3. Why lock dependencies?
4. Why list writable paths?
5. Does build success replace runtime checks?

> [!question]- Answers
> 1. No.
> 2. No.
> 3. Control resolution.
> 4. Grant only required access.
> 5. No.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/08 - Multi-stage Builds|Multi-stage Builds]]
- [[08 - Dockerfile and Compose Mastery/10 - Dockerfile Anti-patterns|Dockerfile Anti-patterns]]

## Official references

- [Best documentation](https://docs.docker.com/build/building/best-practices/)
- [Cache documentation](https://docs.docker.com/build/cache/optimize/)
- [Secret documentation](https://docs.docker.com/build/building/secrets/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/08 - Multi-stage Builds\|Multi-stage Builds]] | [[08 - Dockerfile and Compose Mastery/10 - Dockerfile Anti-patterns\|Dockerfile Anti-patterns]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
