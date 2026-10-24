---
title: "Docker Compose YAML Reference"
subject: "Docker"
level: reference
tags:
  - docker
  - reference
---

# Docker Compose YAML Reference

Read a Compose model as a set of runtime relationships.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Read a Compose model as a set of runtime relationships. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- What do services, ports, volumes, networks, and depends_on mean?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[01 - Docker Fundamentals/04 - Docker Compose|Docker Compose]].

## Introduction, purpose, and how it works

Top-level sections declare project resources. Service-level keys configure how a component uses those resources. A volume declared at the top level still needs a service mount to carry data. Two services can communicate over the default network without publishing ports. A health-based dependency controls startup sequencing, not all future failures.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `services.NAME.image` | Choose an existing image | Use a tested digest for releases. |
| `services.NAME.build` | Select a build context | Use when building locally; include a complete Dockerfile. |
| `services.NAME.command` | Override image CMD | Use with awareness of ENTRYPOINT. |
| `services.NAME.ports` | Publish host access | Bind loopback for local-only services. |
| `services.NAME.environment` | Set runtime variables | Quote ambiguous scalars and protect secrets. |
| `services.NAME.volumes` | Attach storage | Distinguish bind paths from named volumes. |
| `services.NAME.depends_on` | Declare dependency sequencing | Use service_healthy with a meaningful probe when readiness matters. |
| `networks.NAME.internal: true` | Restrict external connectivity for that network | Use deliberate network policy; naming a network internal is insufficient. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```yaml
services:
  reader:
    image: alpine:3.22
    command: ["sh", "-c", "ls -la /data"]
    volumes:
      - sample:/data:ro
    networks:
      - internal
volumes:
  sample:
networks:
  internal:
```

## Step-by-step walkthrough

1. In a scratch directory save compose.yaml and run `docker compose config --quiet`. This validates the model without starting it.

2. The reader service mounts the sample named volume read-only at /data. The top-level volume declaration creates a managed resource when the service is started.

3. The network name here is a logical project resource, not a fixed global container IP. The name internal does not by itself set the network’s internal isolation option.

4. If choosing to run the local demonstration, use `docker compose run --rm reader`; the command lists an initially empty volume and exits.

## Expected result

Validation succeeds; the optional reader lists its mounted directory and cannot write through the mount.

## Practice

Add a second reader service mounting the same volume read-only. Success: distinguish the shared volume declaration from each service’s mount.

## Common errors and troubleshooting

Undefined volume: add the top-level declaration. Unknown field: check Compose plugin version and schema. Host port collision: change the host-side mapping, not necessarily the process’s listening port.

## Knowledge check

1. What declares components?
2. What attaches storage?
3. Does a network name enable special isolation?
4. Is ports needed between peers?
5. What should validate first?

> [!question]- Answers
> 1. services.
> 2. A service volume mount.
> 3. No, properties do.
> 4. No.
> 5. docker compose config.

## Cleanup

After the optional run, docker compose down removes its network. docker compose down --volumes deliberately destroys the empty sample volume; do not apply it to valued data.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[01 - Docker Fundamentals/04 - Docker Compose|Docker Compose]]
- [[07 - References/04 - GitHub Actions YAML Reference|Next in the roadmap: GitHub Actions YAML Reference]]

## Official references

- [Compose official documentation](https://docs.docker.com/reference/compose-file/services/)
- [Health official documentation](https://docs.docker.com/compose/how-tos/startup-order/)
- [Volume official documentation](https://docs.docker.com/engine/storage/volumes/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[07 - References/02 - Dockerfile Instruction Reference\|Dockerfile Instruction Reference]] | [[07 - References/04 - GitHub Actions YAML Reference\|GitHub Actions YAML Reference]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
