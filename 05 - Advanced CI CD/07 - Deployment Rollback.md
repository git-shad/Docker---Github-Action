---
title: "Deployment Rollback"
subject: "GitHub Actions"
level: advanced
tags:
  - github-actions
  - advanced
---

# Deployment Rollback

Restore a known-good artifact only after checking data compatibility.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Restore a known-good artifact only after checking data compatibility. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I roll back a bad container release safely?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[05 - Advanced CI CD/01 - Deployment to Ubuntu Server|Deployment to Ubuntu Server]], [[05 - Advanced CI CD/03 - Docker Image Versioning|Docker Image Versioning]].

## Introduction, purpose, and how it works

Rollback replaces application code with a previous artifact. It does not reverse writes made by the new version or automatically undo database migrations. A destructive migration can make older code incompatible. Favor expand-and-contract schema changes: add compatible structures, migrate data, switch consumers, then remove old structures only after the rollback window. A tested backup restore is a distinct recovery process.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `WEB_IMAGE=PREVIOUS_DIGEST` | Select a known-good artifact | Use only after compatibility review. |
| `docker compose up -d --wait` | Replace and verify the service | This can interrupt a single-instance deployment. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Authorized staging-server operation; changes running services. The walkthrough identifies filenames and the machine where each operation belongs.

```bash
read -r -p "Previous compatible web image digest reference: " WEB_IMAGE
export WEB_IMAGE
docker compose config --quiet
docker compose pull web
docker compose up -d --wait --wait-timeout 120 web
curl --fail --silent --show-error http://127.0.0.1:8080/
```

## Step-by-step walkthrough

1. Run on the authorized disposable staging server in the release directory from Deployment to Ubuntu Server. These commands replace a running service and can cause downtime.

2. Before proceeding, stop further releases, identify the last healthy digest, and check schema/data/configuration compatibility. For a static frontend the main concern may be API compatibility; Laravel also needs schema and queue compatibility.

3. Enter the recorded compatible digest. Pull before switching; the old artifact must still exist and be authorized.

4. Recreate web, wait for readiness, then check HTTP and application behavior. Record the active digest in the persistent release configuration so a later restart does not reapply the bad version.

5. If data was corrupted, follow the separate incident restore procedure instead of assuming code rollback repairs it.

## Expected result

The compatible older image is running and passes runtime checks; affected data is assessed independently.

## Practice

On a disposable staging server, deploy a harmless visual regression and restore the preceding image. Success: verify both displayed version and recorded digest.

## Common errors and troubleshooting

Older image missing: retention policy failed the rollback requirement. Healthy container but broken API calls: check contract compatibility. Do not run automatic down migrations against real data without a reviewed recovery plan.

## Knowledge check

1. Does rollback reverse database writes?
2. What artifact should be retained?
3. What is expand-and-contract?
4. Does health prove API compatibility?
5. What else must be updated?

> [!question]- Answers
> 1. No.
> 2. The known-good digest.
> 3. Gradual compatible schema evolution.
> 4. No.
> 5. The persistent active-release record.

## Cleanup

Keep previous artifacts and incident evidence until retention requirements permit deletion. Do not remove database volumes as part of rollback.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[05 - Advanced CI CD/01 - Deployment to Ubuntu Server|Deployment to Ubuntu Server]]
- [[05 - Advanced CI CD/03 - Docker Image Versioning|Docker Image Versioning]]
- [[05 - Advanced CI CD/08 - Database Data Persistence|Next in the roadmap: Database Data Persistence]]

## Official references

- [Compose official documentation](https://docs.docker.com/reference/compose-file/services/)
- [Laravel official documentation](https://laravel.com/docs/13.x/deployment)
- [Registry official documentation](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[05 - Advanced CI CD/06 - Container Registry Authentication\|Container Registry Authentication]] | [[05 - Advanced CI CD/08 - Database Data Persistence\|Database Data Persistence]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
