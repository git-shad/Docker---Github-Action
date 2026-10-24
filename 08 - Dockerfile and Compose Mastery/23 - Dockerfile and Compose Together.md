---
title: "Dockerfile and Compose Together"
subject: Docker
level: intermediate
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - intermediate
---

# Dockerfile and Compose Together

Build a small image and instantiate it using a clearly separated Compose service.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Build a small image and instantiate it using a clearly separated Compose service.

## Questions answered

- How do I write Dockerfile and compose.yml together from scratch?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/22 - Dockerfile vs Compose|Dockerfile vs Compose]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

Start with the program’s needs: executable, files, current directory, user and output. Write an image that satisfies those needs, then decide instance configuration such as environment and mounts. This process is easier than beginning with a large copied stack. The complete example below uses a non-secret runtime value and stdout, so it needs neither published ports nor persistent data.

## Syntax reference

Image: FROM + WORKDIR + COPY + USER + CMD. Service: build + environment. Explicit shell use belongs in the script when expansion is needed; use exec when handing off to a long-lived child.

## Code examples

**report.sh:**
```bash
#!/bin/sh
set -eu
printf "mode=%s\n" "$APP_MODE"
```

**Dockerfile:**
```dockerfile
FROM alpine:3.22
WORKDIR /app
COPY --chmod=0555 report.sh /app/report.sh
ENV APP_MODE=local
USER 10001:10001
CMD ["/app/report.sh"]
```

**compose.yml:**
```yaml
services:
  report:
    build: .
    environment:
      APP_MODE: training
```

**Optional local sequence:**
```bash
docker compose -f compose.yml config --quiet
docker compose -f compose.yml build report
docker compose -f compose.yml run --rm report
```

**Incorrect expectation:** editing Dockerfile without rebuilding does not change an already-built image.

## Line-by-line walkthrough

1. The script uses portable sh and fails on missing APP_MODE.

2. COPY sets executable permissions while including only the one intended file.

3. ENV gives the standalone image a safe default.

4. USER lowers privilege; the script only reads and writes stdout.

5. Compose constructs the image and overrides APP_MODE for this service instance.

6. The run is one-off; it is not a daemon that should remain running.

## DO ✅ / DON'T ❌

- **DO ✅** Start from program requirements, because every setting should solve a known need.
- **DO ✅** Verify the image standalone and through Compose when later practicing, because each checks a different boundary.

- **DON'T ❌** Add ports or volumes without a consumer, because unnecessary resources make lifecycle harder to understand.
- **DON'T ❌** Assume a short successful container is broken because it exited; one-off work should finish.

## Expected result

Your plan needs no web port, sets a non-root identity, and predicts the runtime override. This is an expected result, not an execution claim.

## Common errors and troubleshooting

Permission denied on startup: check script mode and shebang. “No such file” with a visible script can indicate CRLF line endings or a missing interpreter. An unset variable error is deliberate validation rather than a Docker bug.

## Practice exercise

**Problem and objective:** Write the same pair for a PHP CLI report instead of a shell script.

**Success condition:** Your plan needs no web port, sets a non-root identity, and predicts the runtime override.

**Hint:** A report process can finish normally; no restart loop is required.

## Knowledge check

1. Why no ports?
2. What makes the script executable?
3. Where does APP_MODE change?
4. Should a report daemonize?
5. Why can CRLF break a shebang?

> [!question]- Answers
> 1. No network listener exists.
> 2. COPY --chmod.
> 3. Container environment.
> 4. No.
> 5. The interpreter path may include an invalid carriage return.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/22 - Dockerfile vs Compose|Dockerfile vs Compose]]
- [[08 - Dockerfile and Compose Mastery/24 - Laravel React MariaDB Example|Laravel React MariaDB Example]]

## Official references

- [File documentation](https://docs.docker.com/reference/dockerfile/)
- [Build documentation](https://docs.docker.com/reference/compose-file/build/)
- [Services documentation](https://docs.docker.com/reference/compose-file/services/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/22 - Dockerfile vs Compose\|Dockerfile vs Compose]] | [[08 - Dockerfile and Compose Mastery/24 - Laravel React MariaDB Example\|Laravel React MariaDB Example]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
