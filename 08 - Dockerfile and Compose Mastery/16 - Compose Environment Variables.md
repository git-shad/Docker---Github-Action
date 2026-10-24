---
title: "Compose Environment Variables"
subject: Docker
level: intermediate
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - intermediate
---

# Compose Environment Variables

Trace values from host interpolation through container environment to application configuration.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Trace values from host interpolation through container environment to application configuration.

## Questions answered

- How do .env, env_file, environment, and dollar escaping differ?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/15 - Compose Volumes and Bind Mounts|Compose Volumes and Bind Mounts]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

Compose interpolation substitutes values while resolving the model. A project .env or CLI --env-file can supply interpolation inputs; that does not automatically inject every value into every container. Service env_file populates runtime variables. Service environment overrides env_file, including explicit empty values. Image ENV is a fallback where not overridden. Shell host values can influence interpolation; inspect actual precedence rather than assuming a file always wins.

Laravel can separately load its own .env and cached configuration. A changed Compose file may require container recreation; a Laravel config cache can retain earlier values. Environment variables and rendered config output are not encrypted secrets.

## Syntax reference

Use ${VAR}, ${VAR:-default}, ${VAR-default}, or ${VAR:?error}. The colon forms treat empty as missing where applicable. $$ passes a dollar through Compose for a container shell to interpret. Within Compose YAML, single-quoted text does not provide a universal escape from interpolation; use $$ deliberately.

## Code examples

**Complete compose.yml:**
```yaml
services:
  report:
    image: alpine:3.22
    env_file: [./runtime.env]
    environment:
      APP_MODE: "${APP_MODE:-local}"
      REQUIRE_LABEL: "${LAB_LABEL:?Set LAB_LABEL for this exercise}"
    command: ["sh", "-c", "printf \"%s:%s\\n\" \"$$APP_MODE\" \"$$REQUIRE_LABEL\""]
```

**runtime.env, non-secret:**
```dotenv
APP_MODE=from-env-file
```

**Optional host invocation:**
```bash
LAB_LABEL=practice APP_MODE=review docker compose -f compose.yml run --rm report
```

**Incorrect expectation:** Setting a value only in Compose interpolation .env does not inject it unless the service actually references or imports it.

## Line-by-line walkthrough

1. runtime.env supplies defaults for process variables.

2. The explicit environment APP_MODE replaces the same key from that file.

3. The required LAB_LABEL expression stops resolution before startup when missing or empty.

4. The command explicitly invokes sh, then $$ allows its variables to expand inside the container.

5. The host invocation selects review and practice for interpolation; neither is a secret.

## DO ✅ / DON'T ❌

- **DO ✅** Use required-value checks for essential settings, because an empty credential/target can cause misleading downstream errors.
- **DO ✅** Pass secrets through appropriate restricted files/providers, because environment and config dumps are inspectable.

- **DON'T ❌** Print full docker compose config in shared logs without review, because it can render sensitive values.
- **DON'T ❌** Assume a .env filename makes content secure or Git-ignored automatically.

## Expected result

Your answer explains the explicit environment override rather than relying on env_file alone. This is an expected result, not an execution claim.

## Common errors and troubleshooting

Unexpected emptiness: inspect interpolation source and scope. Different runtime values: inspect environment versus env_file versus application cache. Never “fix” a private value by committing it to YAML.

## Practice exercise

**Problem and objective:** Predict output with the host invocation, then with APP_MODE unset.

**Success condition:** Your answer explains the explicit environment override rather than relying on env_file alone.

**Hint:** The service fallback local still overrides from-env-file when APP_MODE is unset.

## Knowledge check

1. Does interpolation .env inject every value?
2. Which wins: environment or env_file?
3. What does $$ do?
4. What does :? enforce?
5. Are environment values encrypted?

> [!question]- Answers
> 1. No.
> 2. environment.
> 3. Defers dollar expansion past Compose.
> 4. A nonempty required interpolation value.
> 5. No.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/15 - Compose Volumes and Bind Mounts|Compose Volumes and Bind Mounts]]
- [[08 - Dockerfile and Compose Mastery/17 - Compose Depends On and Healthchecks|Compose Depends On and Healthchecks]]

## Official references

- [Env documentation](https://docs.docker.com/compose/how-tos/environment-variables/variable-interpolation/)
- [Services documentation](https://docs.docker.com/reference/compose-file/services/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/15 - Compose Volumes and Bind Mounts\|Compose Volumes and Bind Mounts]] | [[08 - Dockerfile and Compose Mastery/17 - Compose Depends On and Healthchecks\|Compose Depends On and Healthchecks]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
