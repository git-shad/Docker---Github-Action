---
title: "Docker in GitHub Actions"
subject: "GitHub Actions"
level: projects
tags:
  - github-actions
  - projects
---

# Docker in GitHub Actions

Run a disposable container command as a CI step.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Run a disposable container command as a CI step. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How does a workflow use Docker on its runner?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[01 - Docker Fundamentals/07 - Docker Commands|Docker Commands]], [[03 - GitHub Actions Fundamentals/04 - GitHub Runners|GitHub Runners]].

## Introduction, purpose, and how it works

A runner can execute Docker CLI commands against its Engine. This is distinct from a job container, which changes the environment where job steps run, and from a service container, which provides a dependency such as a database. A Docker build needs a build context; a Docker run needs a local or pullable image. These examples use hosted Linux runners rather than Windows or macOS container support.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `docker run --rm IMAGE COMMAND` | Execute a disposable CI process | Use when the image supplies the toolchain. |
| `timeout-minutes: 5` | Bound CI runtime | Use with command-level timeouts for network operations. |

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
  container-command:
    runs-on: ubuntu-24.04
    timeout-minutes: 5
    steps:
      - name: Inspect tooling
        run: docker version
      - name: Run isolated command
        run: docker run --rm alpine:3.22 sh -c 'test -r /etc/os-release && cat /etc/os-release'
```

## Step-by-step walkthrough

1. Save as a practice workflow if later trying it; no checkout is needed because no repository files are consumed.

2. docker version checks Engine access. The next step pulls Alpine if necessary and starts a shell inside the image.

3. test checks readability, and && runs cat only on success. A nonzero container result becomes the Docker CLI result and fails the step.

4. Use immutable image digests for release-critical inputs after reviewing the desired platform; version tags here are readable teaching defaults, not frozen supply-chain pins.

## Expected result

The job prints the container’s OS metadata, which differs from the runner’s own Ubuntu userspace.

## Practice

Change the tested path to a nonexistent file. Success: the container, step, and job fail rather than reporting success.

## Common errors and troubleshooting

Daemon unavailable: verify runner type and tooling. Image pull failure: inspect registry availability, credentials if private, and network/rate limits. Do not give a read-only test package-write permission.

## Knowledge check

1. Where is the Engine?
2. Is this a job container?
3. What propagates failure?
4. Why no checkout?
5. Are tags immutable?

> [!question]- Answers
> 1. On the runner in this example.
> 2. No, it is an explicit docker run step.
> 3. The container command exit code.
> 4. No source files are needed.
> 5. No.

## Cleanup

--rm removes the one-off container. Hosted runner disposal removes remaining local cache after the job.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[01 - Docker Fundamentals/07 - Docker Commands|Docker Commands]]
- [[03 - GitHub Actions Fundamentals/04 - GitHub Runners|GitHub Runners]]
- [[04 - Docker and GitHub Actions/02 - Automatic Docker Builds|Next in the roadmap: Automatic Docker Builds]]

## Official references

- [Ci official documentation](https://docs.docker.com/build/ci/github-actions/)
- [Runner official documentation](https://docs.github.com/en/actions/how-tos/write-workflows/choose-where-workflows-run/choose-the-runner-for-a-job)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[03 - GitHub Actions Fundamentals/08 - Troubleshooting Workflows\|Troubleshooting Workflows]] | [[04 - Docker and GitHub Actions/02 - Automatic Docker Builds\|Automatic Docker Builds]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
