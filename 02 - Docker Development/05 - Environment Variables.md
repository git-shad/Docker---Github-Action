---
title: "Environment Variables"
subject: "Docker"
level: intermediate
tags:
  - docker
  - intermediate
---

# Environment Variables

Trace configuration from Compose interpolation to the application process.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Trace configuration from Compose interpolation to the application process. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do image ENV, Compose .env, and Laravel .env differ?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). PHP examples use PHP 8.4; frontend examples use Node 22; database examples use MariaDB 11.4. Check the project’s own support constraints. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[02 - Docker Development/04 - Laravel and MariaDB Networking|Laravel and MariaDB Networking]].

## Introduction, purpose, and how it works

An environment variable is a string supplied to a process. Dockerfile ENV stores image defaults. Compose’s project .env can supply interpolation values in YAML; it is not automatically injected wholesale into every container. Compose environment and env_file configure container variables. Laravel can also read its own .env from the project filesystem. An external container environment value generally overrides Laravel’s dotenv value. None of these mechanisms encrypts secrets.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `environment:` | Supply process variables | Use for runtime configuration; values may be visible through inspection. |
| `${NAME:-default}` | Substitute a Compose value with a fallback | Use for optional values; require important values explicitly. |
| `$$NAME` | Defer expansion to the container shell | Use in shell commands; JSON exec form alone does not expand variables. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```yaml
services:
  demo:
    image: alpine:3.22
    environment:
      APP_MODE: "${APP_MODE:-local}"
    command:
      - sh
      - -c
      - |
        printf 'mode=%s\n' "$$APP_MODE"
```

## Step-by-step walkthrough

1. In a fresh directory save compose.yaml. Optionally create .env containing APP_MODE=training; this is non-secret configuration.

2. `${APP_MODE:-local}` substitutes a host or project value when Compose reads YAML; local is the fallback.

3. `$$APP_MODE` escapes Compose interpolation so the shell inside the container reads its own environment instead.

4. Run `docker compose config`, then `docker compose run --rm demo`. Never dump config in shared logs when it contains real credentials.

## Expected result

The process prints mode=training if .env supplied that value; otherwise mode=local.

## Practice

Run `APP_MODE=review docker compose run --rm demo`. Success: explain why the host assignment changes the resulting value.

## Common errors and troubleshooting

An empty value may be caused by an unset variable or wrong precedence assumption. YAML booleans and numbers should be quoted for environment values. Build ARG and ENV must not carry secrets into image metadata.

## Knowledge check

1. Is Compose .env automatically a container env_file?
2. What does $$ escape?
3. Does ENV encrypt a value?
4. What supplies fallback local?
5. Where should a build secret go?

> [!question]- Answers
> 1. No.
> 2. Compose interpolation of the dollar sign.
> 3. No.
> 4. The :- interpolation form.
> 5. A supported BuildKit secret mount, not ARG or ENV.

## Cleanup

The one-off container is removed. Remove any lab-only .env only if you created it for this exercise; never commit real secrets.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[02 - Docker Development/04 - Laravel and MariaDB Networking|Laravel and MariaDB Networking]]
- [[02 - Docker Development/06 - Bind Mounts and Hot Reload|Next in the roadmap: Bind Mounts and Hot Reload]]

## Official references

- [Compose official documentation](https://docs.docker.com/reference/compose-file/services/)
- [File official documentation](https://docs.docker.com/reference/dockerfile/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[02 - Docker Development/04 - Laravel and MariaDB Networking\|Laravel and MariaDB Networking]] | [[02 - Docker Development/06 - Bind Mounts and Hot Reload\|Bind Mounts and Hot Reload]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
