---
title: "Common Errors and Solutions"
subject: "GitHub Actions"
level: projects
tags:
  - github-actions
  - projects
---

# Common Errors and Solutions

Triage failures using the first failing boundary instead of broad reset commands.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Triage failures using the first failing boundary instead of broad reset commands. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I distinguish build, network, database, and workflow errors?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[01 - Docker Fundamentals/07 - Docker Commands|Docker Commands]], [[03 - GitHub Actions Fundamentals/08 - Troubleshooting Workflows|Troubleshooting Workflows]].

## Introduction, purpose, and how it works

Diagnosis is narrowing uncertainty. First establish which system failed: YAML parsing, runner allocation, image build, process startup, DNS, authentication, schema, or HTTP routing. Each boundary needs different evidence. Deleting volumes or weakening permissions may hide symptoms while causing data loss or exposing credentials. Prefer read-only inspection before any repair.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `docker context show` | Identify the selected daemon context | Use before diagnosing the wrong machine. |
| `docker compose ps -a` | List running and stopped project containers | Use exited state as evidence, not just absence from running output. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```bash
docker context show
docker compose version
docker compose ps -a
docker compose logs --no-color --tail 50
```

## Step-by-step walkthrough

1. Run in the intended local or authorized staging Compose directory using Ubuntu Bash. These inspect the selected daemon and project; logs may contain sensitive data, so review before sharing.

2. If the daemon is wrong, fix context selection before changing resources. If no config is found, check directory or -f path.

3. For build failures inspect the first failed Dockerfile instruction and context. For exited containers inspect logs and exit status before recreating.

4. For connection failures test DNS, then readiness, then credentials, then schema in that order. Do not fix an access-denied SQL error by publishing port 3306.

5. For Actions failures distinguish invalid workflow syntax, unavailable runner, shell exit, and denied token operation. Return to the specific linked lesson for the smallest relevant correction.

## Expected result

A diagnostic note identifies one likely boundary and a narrowly targeted next check.

## Practice

Reproduce one harmless missing-file error in a scratch project. Success: fix the path without deleting data or changing privileges.

## Common errors and troubleshooting

Port allocated: choose a different host port or identify the current owner. Missing vendor: run Composer in the correct mount. SQL access denied: check initialized account credentials. GHCR denied: inspect package access and job permissions. SSH host mismatch: verify server identity independently.

## Knowledge check

1. What is the first goal?
2. Should volume deletion be a default fix?
3. Does publishing a DB port fix passwords?
4. Should SSH host checks be disabled?
5. Why bound logs?

> [!question]- Answers
> 1. Identify the failing boundary.
> 2. No.
> 3. No.
> 4. No.
> 5. Reduce noise and unnecessary data exposure.

## Cleanup

Inspection creates no resources. Restore only deliberate sandbox faults; preserve important incident evidence.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[01 - Docker Fundamentals/07 - Docker Commands|Docker Commands]]
- [[03 - GitHub Actions Fundamentals/08 - Troubleshooting Workflows|Troubleshooting Workflows]]
- [[07 - References/01 - Docker Command Cheat Sheet|Next in the roadmap: Docker Command Cheat Sheet]]

## Official references

- [Logs official documentation](https://docs.github.com/en/actions/how-tos/monitor-workflows/use-workflow-run-logs)
- [Compose official documentation](https://docs.docker.com/reference/compose-file/services/)
- [Network official documentation](https://docs.docker.com/engine/network/)
- [Registry official documentation](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[06 - Practical Examples/05 - CI CD Pipeline Walkthrough\|CI CD Pipeline Walkthrough]] | [[07 - References/01 - Docker Command Cheat Sheet\|Docker Command Cheat Sheet]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
