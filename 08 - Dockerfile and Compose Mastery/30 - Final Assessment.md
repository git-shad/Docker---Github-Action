---
title: "Final Assessment"
subject: Docker
level: advanced
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - advanced
---

# Final Assessment

Demonstrate independent design, causal debugging, and review of Docker configuration.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Demonstrate independent design, causal debugging, and review of Docker configuration.

## Questions answered

- Can I write, explain, and review a Dockerfile and Compose model without copying blindly?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/24 - Laravel React MariaDB Example|Laravel React MariaDB Example]], [[08 - Dockerfile and Compose Mastery/25 - Development vs Production|Development vs Production]], [[08 - Dockerfile and Compose Mastery/26 - Debugging Docker Configurations|Debugging Docker Configurations]], [[08 - Dockerfile and Compose Mastery/27 - Security and Secrets|Security and Secrets]], [[08 - Dockerfile and Compose Mastery/28 - Practice Exercises|Practice Exercises]], [[08 - Dockerfile and Compose Mastery/29 - Common Errors and Solutions|Common Errors and Solutions]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

The assessment rewards a justified model rather than memorized keywords. You must explain inputs, phases, defaults, network scope, data lifetime, secret consumers and expected observations. Where a real application requirement is unknown, state the prerequisite rather than inventing it. Review the complete worked examples only after drafting your own answer.

## Syntax reference

Use Dockerfile syntax for image construction and modern Compose Specification mappings for runtime instances. Every submitted file must have its intended filename, context and role. Label schematic fragments and unexecuted expectations explicitly.

## Code examples

**Answer-planning format:**
```text
Requirement -> configuration location -> accepted type -> reason
Changed input -> rebuild/recreate/reload effect
Observed error -> likely boundary -> smallest safe check
Resource -> owner -> lifetime -> cleanup/recovery consequence
```

**Incorrect answer pattern:** “It is production-ready because docker compose config parses.” Schema validity does not demonstrate runtime behavior or operational readiness.

## Line-by-line walkthrough

1. Draft requirements before viewing worked answers.

2. Complete the six tasks in the assessment brief.

3. For each configuration line, name phase, accepted shape, omitted behavior and why this value was chosen.

4. Score the answer using the rubric and critical-error criteria.

5. Revisit the exact weak lesson before repeating; no real deployment is required to demonstrate configuration reasoning.

## DO ✅ / DON'T ❌

- **DO ✅** Identify unknown application prerequisites, because honesty about missing facts is part of a correct design.
- **DO ✅** Use observable acceptance criteria, because a plausible configuration is still only a prediction until tested in its intended environment.

- **DON'T ❌** Award full credit for pasted code without explanation, because the purpose is independent reasoning.
- **DON'T ❌** Run against real data merely to complete a knowledge check, because this assessment is documentation-first.

## Expected result

At least 80/100 with no critical misconceptions and clear reasons for each major configuration choice. This is an expected result, not an execution claim.

## Common errors and troubleshooting

If you cannot explain final argv or data deletion, revisit startup and storage lessons. If a diagram uses one hostname everywhere, revisit address scopes. If a secret is only “hidden in .env”, revisit source/grant/consumer limitations.

## Practice exercise

**Problem and objective:** Complete all six assessment tasks and score the result before opening the answer guide.

**Success condition:** At least 80/100 with no critical misconceptions and clear reasons for each major configuration choice.

**Hint:** The model answer points to complete prerequisites; it never pretends unknown application details are verified.

## Knowledge check

1. Does config success prove production readiness?
2. What should unknown project details become?
3. What is the primary assessment skill?
4. Can a red deploy have changed runtime?
5. Does rollback automatically repair database writes?

> [!question]- Answers
> 1. No.
> 2. Explicit prerequisites or open questions, not invented facts.
> 3. Explaining why each setting belongs and what it changes.
> 4. Yes, inspect the last completed action.
> 5. No.

## Assessment brief

Design a local configuration for an existing Laravel backend, React TypeScript frontend, nginx entry point, and MariaDB database. Do not execute it for this assessment unless separately choosing a disposable lab. State the project prerequisites instead of inventing route names, credentials, PHP extensions, or dependency support.

Submit Markdown containing your proposed files, lifecycle annotations, request trace, diagnostic plan, and safety review. There is no required real registry or deployment target.

### Task 1 — Build inputs and recipe (20 points)

Write a frontend multi-stage recipe and a backend runtime recipe. Identify contexts, lock files, ignored files, installed extensions, final image contents, and runtime identities. Explain how a source-only edit versus a lock-file edit changes caching.

### Task 2 — Runtime model (20 points)

Write Compose service boundaries, only the needed host port, service DNS names, persistent database data, required interpolation checks, and explicit command/entrypoint behavior. Explain every mapping/list location.

### Task 3 — Configuration and secrecy (15 points)

Separate build parameters, ordinary runtime configuration, interpolation inputs, application .env loading and secret files. Explain exactly how the backend consumes its password instead of assuming universal _FILE support.

### Task 4 — Readiness and lifecycle (15 points)

Choose probes and bounded waits, distinguish service_started from service_healthy, and explain process exit versus unhealthy state. Compare build, up, run, exec, stop and down for your model.

### Task 5 — Diagnose changes (15 points)

Explain how to diagnose a missing COPY input, a bind mount hiding dependencies, a SQL login failure after password-file edits, a frontend /api 502, and a mistaken localhost database hostname. Preserve data and avoid blanket permission changes.

### Task 6 — Review and recovery (15 points)

List development assumptions, production requirements, merge hazards, resource enforcement limits, secret rotation, backups, and rollback compatibility. Predict exactly what down and down --volumes would remove.

## Marking rubric

| Area | Full-credit evidence |
| --- | --- |
| Correct phase and scope | Each setting is attached to the right lifecycle and hierarchy. |
| Reproducibility | Base/dependency identity and update policy are explicit. |
| Input control | Context and ignore rules preserve needed files while excluding sensitive/local output. |
| Startup reasoning | Final executable/arguments and signal path are explained. |
| Data and permissions | Required writes are narrow and durable data is independent of container instances. |
| Connectivity | Host/browser/service address scopes are distinct. |
| Secrecy | Credential source, grant, consumer and limitations are explained. |
| Verification honesty | Predictions and actual execution evidence remain separate. |

Score each task against the stated points. A passing self-assessment needs at least 80/100 and no critical misunderstanding: public database exposure without a requirement, real secrets in image content/browser output, routine volume deletion to repair access, or claiming that a development lab is production-ready without addressing its missing requirements.

> [!example]- Worked design and answer guide
> **Recipe answer:** Use the frontend design from [[08 - Dockerfile and Compose Mastery/08 - Multi-stage Builds|Multi-stage Builds]]: copy manifests before source, install locked dependencies, enforce the project’s typecheck/build scripts, and copy only dist into nginx. Base digests should be real reviewed references in a release, with an update policy. For backend development, prepare PHP and required extensions, install Composer, mount the disposable source checkout, and use an explicit secret-reading wrapper; the complete configuration is in [[08 - Dockerfile and Compose Mastery/24 - Laravel React MariaDB Example|the full-stack example]]. Explain why a production PHP runtime would need its own image source/dependency and serving plan.
>
> **Runtime answer:** frontend is the only host-published HTTP entry point, bound to loopback for the lab. nginx forwards real API paths to app:8000; app connects to db:3306. db-data mounts /var/lib/mysql. No host database publication is required. The browser never receives database credentials or a db hostname.
>
> **Secret answer:** MariaDB consumes its documented password-file variables. Laravel receives DB_PASSWORD from a wrapper that reads its service-granted secret and then execs the official PHP entrypoint with the requested argv. Local file sources are not automatically encrypted; host permissions, untracked files, trusted consumers and rotation still matter. A compromised application process can access its own credentials.
>
> **Readiness answer:** MariaDB’s supported helper measures connection and storage readiness; service_healthy gates startup. A service_started frontend dependency on app does not prove API readiness. Add an application-specific probe only after verifying a real endpoint and its meaning. Restart policy is not continuous health supervision.
>
> **Diagnostic answer:** A missing source starts with context and ignore checks. Missing vendor after mounting /app points to mount masking. Changed password files against an existing database require account-state review, not volume deletion. A 502 requires upstream listener/protocol/readiness evidence; localhost inside app points to app itself rather than db.
>
> **Recovery answer:** down removes the selected project containers and networks, keeping named data; down --volumes can permanently erase it. Persisted schema/data and Laravel APP_KEY constrain rollback. Production additionally needs a supported app web tier, debug disabled, immutable reviewed artifacts, TLS, resource verification, monitoring, backups/restores, and a compatible release process.

## Independent miniature answer

For a fully self-contained smaller solution, these two files build and serve static content. They intentionally do not claim to implement Laravel, authentication, or a database. They demonstrate correct context, startup inheritance, port scope, and a meaningful local HTTP probe.

```dockerfile
FROM nginx:stable-alpine
COPY index.html /usr/share/nginx/html/index.html
EXPOSE 80
```

**index.html:**
```html
<h1>assessment artifact</h1>
```

**compose.yml:**
```yaml
services:
  web:
    build: .
    ports: ["127.0.0.1:8080:80"]
    healthcheck:
      test: ["CMD-SHELL", "wget -q -O /dev/null http://127.0.0.1/ || exit 1"]
      interval: 5s
      timeout: 3s
      retries: 12
```


## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/24 - Laravel React MariaDB Example|Laravel React MariaDB Example]]
- [[08 - Dockerfile and Compose Mastery/25 - Development vs Production|Development vs Production]]
- [[08 - Dockerfile and Compose Mastery/26 - Debugging Docker Configurations|Debugging Docker Configurations]]
- [[08 - Dockerfile and Compose Mastery/27 - Security and Secrets|Security and Secrets]]
- [[08 - Dockerfile and Compose Mastery/28 - Practice Exercises|Practice Exercises]]
- [[08 - Dockerfile and Compose Mastery/29 - Common Errors and Solutions|Common Errors and Solutions]]
- [[Docker/04 - Advanced/Docker Daemon Flags and Configuration|Next in the roadmap: Docker Daemon Flags and Configuration]]

## Official references

- [File documentation](https://docs.docker.com/reference/dockerfile/)
- [Compose documentation](https://docs.docker.com/reference/compose-file/)
- [Best documentation](https://docs.docker.com/build/building/best-practices/)
- [Services documentation](https://docs.docker.com/reference/compose-file/services/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/29 - Common Errors and Solutions\|Common Errors and Solutions]] | [[Docker/04 - Advanced/Docker Daemon Flags and Configuration\|Docker Daemon Flags and Configuration]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
