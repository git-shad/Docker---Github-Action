---
title: "Container Cleanup"
subject: "GitHub Actions"
level: projects
tags:
  - github-actions
  - projects
---

# Container Cleanup

Scope cleanup to one project and retain useful failure evidence.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Scope cleanup to one project and retain useful failure evidence. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I clean up CI containers without deleting unrelated data?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[01 - Docker Fundamentals/04 - Docker Compose|Docker Compose]], [[04 - Docker and GitHub Actions/04 - Docker Compose in CI|Docker Compose in CI]].

## Introduction, purpose, and how it works

Docker resources can outlive a failed command. Explicit project naming prevents Compose cleanup from removing another stack on a shared daemon. Hosted runner disposal is a useful final boundary, but self-hosted systems need a reliable external cleanup policy as well. Cleanup should not change a failed test into a successful job.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `--remove-orphans` | Remove services no longer defined in this project | Use only with a correctly scoped project. |
| `--volumes` | Delete attached project volumes | Use for disposable CI data, not persistent environments. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** CI demonstration in a practice repository. The walkthrough identifies filenames and the machine where each operation belongs.

```yaml
name: Learning example
on:
  workflow_dispatch:
permissions:
  contents: read
jobs:
  cleanup-demo:
    runs-on: ubuntu-24.04
    env:
      COMPOSE_PROJECT_NAME: clean-${{ github.run_id }}-${{ github.run_attempt }}
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
        with:
          persist-credentials: false
      - run: docker compose up -d --wait --wait-timeout 120
      - name: Bounded failure evidence
        if: failure()
        run: docker compose logs --no-color --tail 100
      - name: Delete disposable project resources
        if: always()
        run: docker compose down --volumes --remove-orphans
```

## Step-by-step walkthrough

1. Use a practice repository containing the one-service nginx compose.yaml from Docker Compose. The last command destroys volumes associated with this project; never point the demonstration at an important stack.

2. The project-name environment value remains consistent across every step. The startup command is bounded.

3. Failure logs run before cleanup so container evidence still exists. Logs may contain application data; use synthetic lab data and review retention.

4. The final step removes project containers, networks, volumes, and orphaned services. It does not invoke global docker system prune.

## Expected result

The project is removed after the job, even when an earlier step fails and the runner is still operational.

## Practice

Add a deliberate failing step after startup in a practice run. Success: cleanup runs and the overall job remains failed.

## Common errors and troubleshooting

Cleanup misses resources: verify project name and compose file selection stayed identical. Runner loss: final steps cannot execute; self-hosted operators need external resource reconciliation.

## Knowledge check

1. Why not global prune?
2. Why logs before down?
3. Can always survive machine loss?
4. What identifies this project?
5. Does cleanup erase a prior test failure?

> [!question]- Answers
> 1. It can delete unrelated resources.
> 2. Removal loses container evidence.
> 3. No.
> 4. COMPOSE_PROJECT_NAME.
> 5. No.

## Cleanup

The cleanup command is the lesson’s destructive action. It is appropriate only for a job-owned disposable project.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[01 - Docker Fundamentals/04 - Docker Compose|Docker Compose]]
- [[04 - Docker and GitHub Actions/04 - Docker Compose in CI|Docker Compose in CI]]
- [[04 - Docker and GitHub Actions/09 - Publishing Docker Images|Next in the roadmap: Publishing Docker Images]]

## Official references

- [Compose official documentation](https://docs.docker.com/reference/compose-file/services/)
- [Ci official documentation](https://docs.docker.com/build/ci/github-actions/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[04 - Docker and GitHub Actions/07 - React TypeScript Build Validation\|React TypeScript Build Validation]] | [[04 - Docker and GitHub Actions/09 - Publishing Docker Images\|Publishing Docker Images]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
