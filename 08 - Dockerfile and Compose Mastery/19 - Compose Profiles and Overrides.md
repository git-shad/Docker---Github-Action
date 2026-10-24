---
title: "Compose Profiles and Overrides"
subject: Docker
level: advanced
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - advanced
---

# Compose Profiles and Overrides

Use optional services and explicit file overlays without accidental configuration carryover.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Use optional services and explicit file overlays without accidental configuration carryover.

## Questions answered

- How do profiles and multiple Compose files change the final model?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/18 - Compose Commands and Entrypoints|Compose Commands and Entrypoints]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

A profile selectively enables services; unprofiled services normally remain active. Explicitly targeting a profiled service can run it and required dependencies without enabling every peer in that profile. Profiles are convenience switches, not access control.

Multiple -f files merge in order. Mappings combine with later values overriding conflicts. Sequences usually append, but command, entrypoint and healthcheck.test replace rather than append. Ports, volumes, secrets and configs have special uniqueness rules; volume targets can replace the prior mount at the same destination. Paths are based on the first/base file, not automatically the overlay directory. Interpolation occurs per file before merge.

## Syntax reference

Use docker compose -f compose.yml -f compose.dev.yml config to inspect the result. Supported !reset tags clear attributes; !override replaces instead of normal merge and requires Compose 2.24.4 or newer. They are Compose custom tags, not portable generic YAML features.

## Code examples

**Base compose.yml:**
```yaml
services:
  web:
    image: nginx:stable-alpine
    ports: ["127.0.0.1:8080:80"]
  inspect:
    image: alpine:3.22
    profiles: [tools]
    command: ["cat", "/etc/os-release"]
```

**compose.dev.yml:**
```yaml
services:
  web:
    ports: !override
      - "127.0.0.1:8081:80"
```

**Optional model inspection:**
```bash
docker compose -f compose.yml -f compose.dev.yml config --quiet
docker compose --profile tools -f compose.yml config --services
```

**Incorrect expectation:** merely adding a second ordinary ports list can retain both 8080 and 8081; changing a published port gives a distinct uniqueness key. Use the explicit replacement above when that is your intent.

## Line-by-line walkthrough

1. The base defines a long-running web and an optional diagnostic tool.

2. The overlay names the same web service.

3. !override replaces the port collection instead of adding another mapping.

4. The explicit -f order fixes both precedence and base path.

5. config resolves the model; it does not start the services.

6. Inspect the full rendered model privately when needed to verify removed mounts and ports, not just parser success.

## DO ✅ / DON'T ❌

- **DO ✅** Select the exact file set explicitly for releases, because an unnoticed development override can expose source or debug ports.
- **DO ✅** Check merged ports and mount targets, because merge is not generic deep replacement.

- **DON'T ❌** Use profiles as a security boundary, because users can enable them.
- **DON'T ❌** Assume generic YAML tools understand Compose tags, because they require a custom loader or Compose itself.

## Expected result

Your resolved model has only one intended published binding. This is an expected result, not an execution claim.

## Common errors and troubleshooting

Unknown !override indicates an incompatible tool/parser. Both ports still present usually means normal sequence merging occurred. Wrong bind path often comes from misunderstanding base-file-relative resolution.

## Practice exercise

**Problem and objective:** Replace a development host port while keeping the base file unchanged.

**Success condition:** Your resolved model has only one intended published binding.

**Hint:** Use explicit override semantics or design a base without development ports.

## Knowledge check

1. Are profiles authorization?
2. What decides overlay precedence?
3. Do all lists append?
4. Where are relative paths anchored?
5. What version supports !override?

> [!question]- Answers
> 1. No.
> 2. The -f order.
> 3. No, exceptions and resource uniqueness apply.
> 4. Normally the first/base file directory.
> 5. Compose 2.24.4 or newer.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/18 - Compose Commands and Entrypoints|Compose Commands and Entrypoints]]
- [[08 - Dockerfile and Compose Mastery/20 - Compose Best Practices|Compose Best Practices]]

## Official references

- [Merge documentation](https://docs.docker.com/reference/compose-file/merge/)
- [Profile documentation](https://docs.docker.com/compose/how-tos/profiles/)
- [Fragment documentation](https://docs.docker.com/reference/compose-file/fragments/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/18 - Compose Commands and Entrypoints\|Compose Commands and Entrypoints]] | [[08 - Dockerfile and Compose Mastery/20 - Compose Best Practices\|Compose Best Practices]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
