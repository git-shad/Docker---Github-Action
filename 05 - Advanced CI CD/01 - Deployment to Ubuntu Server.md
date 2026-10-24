---
title: "Deployment to Ubuntu Server"
subject: "GitHub Actions"
level: advanced
tags:
  - github-actions
  - advanced
---

# Deployment to Ubuntu Server

Replace a single-host web service and verify its HTTP response.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Replace a single-host web service and verify its HTTP response. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I deploy a tested static web image to an Ubuntu server?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[02 - Docker Development/07 - Multi-stage Docker Builds|Multi-stage Docker Builds]], [[02 - Docker Development/09 - Development vs Production|Development vs Production]], [[04 - Docker and GitHub Actions/09 - Publishing Docker Images|Publishing Docker Images]].

## Introduction, purpose, and how it works

Deployment changes a running environment. This lesson is a single-host staging exercise for the nginx frontend image, not a complete Laravel production deployment. It assumes an Ubuntu 24.04 server that you control, Docker Engine and Compose v2 already provisioned, SSH key authentication, a non-root deployment account with reviewed Docker access, and a verified SSH host key. Docker access is effectively powerful host access. TLS, reverse proxying, host patching, firewall rules, monitoring, and backups are separate production requirements.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `WEB_IMAGE` | Choose an immutable release artifact | Set to the actual tested frontend digest. |
| `docker compose pull web` | Download a candidate | Use before switching a service. |
| `docker compose up -d --wait` | Reconcile and wait for readiness | Use with meaningful image health probes. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Authorized staging-server operation; changes running services. The walkthrough identifies filenames and the machine where each operation belongs.

```yaml
services:
  web:
    image: "${WEB_IMAGE:?Set a tested image reference}"
    ports:
      - "127.0.0.1:8080:80"
    restart: unless-stopped
    healthcheck:
      test: ["CMD-SHELL", "wget -q -O /dev/null http://127.0.0.1:80/ || exit 1"]
      interval: 5s
      timeout: 3s
      retries: 12
      start_period: 10s
```

## Step-by-step walkthrough

1. On the authorized staging server, create a dedicated directory such as ~/lesson-release and save this block as compose.yaml. The commands below are run there in Ubuntu Bash, not on the Actions runner. This publishes only loopback port 8080.

2. Obtain a tested frontend image digest from your registry. Authenticate with a read-only account first if the image is private; see Container Registry Authentication. Set WEB_IMAGE to that real image@sha256:digest value in your shell. Do not invent a digest or use the lesson-cat command image here.

3. Run `docker compose config --quiet`, then `docker compose pull web`. This downloads the candidate without yet replacing the running service. Record the previous release digest before proceeding.

4. Run `docker compose up -d --wait --wait-timeout 120 web`. This may stop and recreate the service; single-instance Compose does not guarantee zero downtime.

5. Run `curl --fail --silent --show-error http://127.0.0.1:8080/` on the server. Add external checks through your configured TLS proxy before declaring a real release successful.

6. For durable restart configuration, record the chosen non-secret WEB_IMAGE value in this directory’s protected release .env file or an operator-managed equivalent. Ensure future commands use the same value.

## Expected result

The staging server serves the chosen frontend image on loopback and passes both container health and an HTTP smoke check.

## Practice

Deploy two visibly different practice frontend versions, recording each digest. Success: identify the active version and restore the older compatible one.

## Common errors and troubleshooting

Image pull denied: review registry access. Probe fails: the chosen runtime must contain wget and serve HTTP on port 80. Curl locally works but public URL fails: inspect proxy, TLS, DNS, and firewall independently.

## Knowledge check

1. Where are commands run?
2. Is loopback publicly reachable?
3. Does pull replace containers?
4. Does Compose guarantee zero downtime?
5. What identifies the exact release?

> [!question]- Answers
> 1. On the authorized server.
> 2. Not without a proxy or tunnel.
> 3. No.
> 4. No.
> 5. The tested image digest.

## Cleanup

Only on a disposable staging server, docker compose down stops this service and removes its network. Do not shut down a shared production stack for a lesson. No volume deletion is required.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[02 - Docker Development/07 - Multi-stage Docker Builds|Multi-stage Docker Builds]]
- [[02 - Docker Development/09 - Development vs Production|Development vs Production]]
- [[04 - Docker and GitHub Actions/09 - Publishing Docker Images|Publishing Docker Images]]
- [[05 - Advanced CI CD/02 - GitHub Container Registry|Next in the roadmap: GitHub Container Registry]]

## Official references

- [Ubuntu official documentation](https://docs.docker.com/engine/install/ubuntu/)
- [Compose official documentation](https://docs.docker.com/reference/compose-file/services/)
- [Health official documentation](https://docs.docker.com/compose/how-tos/startup-order/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[04 - Docker and GitHub Actions/10 - Preventing Failed Deployments\|Preventing Failed Deployments]] | [[05 - Advanced CI CD/02 - GitHub Container Registry\|GitHub Container Registry]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
