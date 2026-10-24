---
title: "Running Containers in CI"
subject: "GitHub Actions"
level: projects
tags:
  - github-actions
  - projects
---

# Running Containers in CI

Build a small command image and assert its output in CI.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Build a small command image and assert its output in CI. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I verify a built image actually runs?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[01 - Docker Fundamentals/03 - Dockerfile Instructions|Dockerfile Instructions]], [[04 - Docker and GitHub Actions/02 - Automatic Docker Builds|Automatic Docker Builds]].

## Introduction, purpose, and how it works

A runtime test exercises the startup command rather than only image construction. Use a deterministic assertion: a job should fail when output or behavior differs. Detached containers need explicit readiness checks; short command containers can return their result directly. Test the exact built image so a separate rebuild cannot change the candidate.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `actual="$(COMMAND)"` | Capture command output in Bash | Use quoted comparisons; substitution trims trailing newlines. |
| `test "$actual" = "expected"` | Assert equality | Use to fail on mismatches; expected output must be meaningful. |

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
  smoke:
    runs-on: ubuntu-24.04
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
        with:
          persist-credentials: false
      - run: docker build -t lesson-cat:ci .
      - name: Assert startup output
        run: |
          actual="$(docker run --rm lesson-cat:ci)"
          test "$actual" = "built into image"
```

## Step-by-step walkthrough

1. Use the complete lesson-cat Dockerfile and greeting.txt from Dockerfile Instructions. They must be committed at repository root.

2. Build once and tag the result locally. The startup default prints the image’s message file.

3. Bash command substitution captures output, trimming trailing newlines. test compares the exact expected text and returns nonzero on mismatch.

4. Avoid piping a test through commands that mask its exit code. For web images use an HTTP smoke check plus service health instead of this text assertion.

## Expected result

The runtime assertion passes only when the built default process prints built into image.

## Practice

Change the build-time message deliberately. Success: the runtime assertion detects the behavior change even though the build succeeds.

## Common errors and troubleshooting

Empty output: inspect ENTRYPOINT and CMD. Permission or architecture error: verify executable mode and platform. An assertion written as echo alone does not enforce anything.

## Knowledge check

1. What extra behavior is tested?
2. Why build once?
3. What trims trailing newlines?
4. Does echo enforce equality?
5. What should an assertion do on mismatch?

> [!question]- Answers
> 1. Container startup output.
> 2. To test the exact candidate.
> 3. Bash command substitution.
> 4. No.
> 5. Return nonzero.

## Cleanup

The command container removes itself. The local image exists only for this hosted job.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[01 - Docker Fundamentals/03 - Dockerfile Instructions|Dockerfile Instructions]]
- [[04 - Docker and GitHub Actions/02 - Automatic Docker Builds|Automatic Docker Builds]]
- [[04 - Docker and GitHub Actions/04 - Docker Compose in CI|Next in the roadmap: Docker Compose in CI]]

## Official references

- [Ci official documentation](https://docs.docker.com/build/ci/github-actions/)
- [File official documentation](https://docs.docker.com/reference/dockerfile/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[04 - Docker and GitHub Actions/02 - Automatic Docker Builds\|Automatic Docker Builds]] | [[04 - Docker and GitHub Actions/04 - Docker Compose in CI\|Docker Compose in CI]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
