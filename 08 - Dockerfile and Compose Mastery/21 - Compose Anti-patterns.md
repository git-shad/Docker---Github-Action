---
title: "Compose Anti-patterns"
subject: Docker
level: intermediate
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - intermediate
---

# Compose Anti-patterns

Recognize Compose mistakes that can leak data, erase state, or create false readiness.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Recognize Compose mistakes that can leak data, erase state, or create false readiness.

## Questions answered

- Which Compose anti-patterns should I avoid?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/20 - Compose Best Practices|Compose Best Practices]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

A configuration can parse while doing the wrong thing. localhost for a database peer, public host bindings, hardcoded credentials, unconditional dependency assumptions and destructive cleanup are all examples. Diagnose the faulty boundary rather than broadening permissions or deleting state. Persistent data deserves a separate recovery process.

## Syntax reference


| Anti-pattern | Correction | Reason |
| --- | --- | --- |
| DB_HOST=localhost in app | DB_HOST=db on a shared network | Loopback is container-local. |
| Publish database as 3306:3306 | No DB publication unless specifically needed | Peers use private container connectivity. |
| Tracked password literal | Restricted runtime secret source | YAML is not secret storage. |
| depends_on alone proves SQL readiness | Declare/probe health then gate on it | Started differs from initialized. |
| down -v to fix login | Inspect initialized account state | Volume deletion loses data. |
| privileged: true by default | Only demonstrated narrow capability needs | Host access must be justified. |


## Code examples

**Incorrect exposure fragment:**
```yaml
services:
  db:
    image: mariadb:11.4
    ports: ["3306:3306"]
```

Omitting the host IP commonly binds all interfaces. This is also incomplete initialization configuration; it is a review fragment.

**Correct complete local-only database model:**
```yaml
services:
  db:
    image: mariadb:11.4
    environment:
      MARIADB_ROOT_PASSWORD: "${LAB_DB_ROOT_PASSWORD:?Set a disposable lab password}"
    volumes: ["db-data:/var/lib/mysql"]
volumes:
  db-data: {}
```

No host database port is needed for peer applications. Environment is a simple lab mechanism, not encrypted secret storage; use the file-secret pattern in [[08 - Dockerfile and Compose Mastery/27 - Security and Secrets|Security and Secrets]] for sensitive runtime configuration.

## Line-by-line walkthrough

1. The bad fragment exposes a powerful data service on unspecified host interfaces.

2. The corrected model requires a local value rather than embedding a real password.

3. Its named volume preserves the actual database data path.

4. No ports entry means no host publication, although attached peers can still connect.

5. Changing first-initialization environment later does not necessarily change existing database accounts.

## DO ✅ / DON'T ❌

- **DO ✅** Inspect effective config before changing runtime, because wrong project or overlay can target unexpected resources.
- **DO ✅** Preserve and back up data before repair, because database state is not disposable just because containers are.

- **DON'T ❌** Run down -v as routine troubleshooting, because it can permanently erase storage.
- **DON'T ❌** Use an allow-all permission change to repair an unknown access error, because it hides the cause and expands risk.

## Expected result

Your review names four independent corrections with reasons and preserves existing database data. This is an expected result, not an execution claim.

## Common errors and troubleshooting

Access denied can be old initialized credentials, wrong user grants, or wrong target. Connection refused is a different failure. Identify which one occurred before editing exposure or deleting storage.

## Practice exercise

**Problem and objective:** Repair a model with localhost DB_HOST, public DB port, hardcoded secret and no readiness.

**Success condition:** Your review names four independent corrections with reasons and preserves existing database data.

**Hint:** Network addressing, exposure, secrecy and readiness are separate concerns.

## Knowledge check

1. Is localhost another service?
2. Does no ports mean no peer connectivity?
3. Does changing init env reset old passwords?
4. What can down -v delete?
5. Is YAML validity enough?

> [!question]- Answers
> 1. No.
> 2. No.
> 3. Not normally.
> 4. Project persistent volumes.
> 5. No.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/20 - Compose Best Practices|Compose Best Practices]]
- [[08 - Dockerfile and Compose Mastery/22 - Dockerfile vs Compose|Dockerfile vs Compose]]

## Official references

- [Services documentation](https://docs.docker.com/reference/compose-file/services/)
- [Volume documentation](https://docs.docker.com/reference/compose-file/volumes/)
- [Health documentation](https://docs.docker.com/compose/how-tos/startup-order/)
- [Secrets documentation](https://docs.docker.com/reference/compose-file/secrets/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/20 - Compose Best Practices\|Compose Best Practices]] | [[08 - Dockerfile and Compose Mastery/22 - Dockerfile vs Compose\|Dockerfile vs Compose]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
