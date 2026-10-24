---
title: "React TypeScript Build Validation"
subject: "GitHub Actions"
level: projects
tags:
  - github-actions
  - projects
---

# React TypeScript Build Validation

Install locked Node dependencies and enforce type checking and a production build.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Install locked Node dependencies and enforce type checking and a production build. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I validate a React TypeScript frontend in CI?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[02 - Docker Development/02 - PHP Composer and Node.js|PHP Composer and Node.js]], [[03 - GitHub Actions Fundamentals/06 - Uses vs Run|Uses vs Run]].

## Introduction, purpose, and how it works

TypeScript checks source types before browser execution. Some bundlers transpile TypeScript without type checking, so a successful Vite bundle alone can miss type errors. Define an explicit typecheck script appropriate for the project. A build verifies packaging, not user behavior or backend compatibility; unit and browser tests are additional checks.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `npm run typecheck` | Execute a project-defined type validation script | Ensure the script exists and matches the tsconfig structure. |
| `-w /app` | Set the container working directory | Use so npm reads the intended package.json. |

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
  frontend:
    runs-on: ubuntu-24.04
    timeout-minutes: 10
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
        with:
          persist-credentials: false
      - name: Install, typecheck, and build
        run: |
          docker run --rm --mount type=bind,src="$GITHUB_WORKSPACE",dst=/app -w /app node:22-bookworm-slim sh -c 'npm ci && npm run typecheck && npm run build'
```

## Step-by-step walkthrough

1. Use an existing React TypeScript project with package-lock.json and build/typecheck scripts in repository root. For a single-tsconfig project, typecheck can run `tsc --noEmit`; for project references use the project’s intended `tsc -b` configuration.

2. Checkout supplies the workspace. The Node image provides the same major toolchain used by the local frontend build lesson. Check package engines before choosing a version.

3. The bind mount exposes source; -w selects the project. npm ci replaces node_modules in the disposable hosted workspace.

4. The && chain stops at the first failure. Public build-time API URLs may be supplied as configuration; never pass server credentials into Vite variables.

## Expected result

Type checking and build both pass, or the first failing command fails the workflow.

## Practice

Introduce an obvious type mismatch in a practice branch. Success: typecheck rejects it before the build can be treated as a release candidate.

## Common errors and troubleshooting

Missing typecheck script: define it based on actual tsconfig layout. Case mismatch in imports may fail on Linux despite working on a case-insensitive host. Lockfile drift should be fixed through reviewed dependency changes.

## Knowledge check

1. Does Vite always typecheck?
2. Why npm ci?
3. What stops the command chain?
4. Are VITE variables private?
5. Does build prove browser behavior?

> [!question]- Answers
> 1. No.
> 2. Lockfile consistency.
> 3. A nonzero exit before the next &&.
> 4. No.
> 5. No.

## Cleanup

The Node container removes itself. Hosted runner disposal removes its workspace; no artifacts are uploaded in this example.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[02 - Docker Development/02 - PHP Composer and Node.js|PHP Composer and Node.js]]
- [[03 - GitHub Actions Fundamentals/06 - Uses vs Run|Uses vs Run]]
- [[04 - Docker and GitHub Actions/08 - Container Cleanup|Next in the roadmap: Container Cleanup]]

## Official references

- [Ci official documentation](https://docs.docker.com/build/ci/github-actions/)
- [Multi official documentation](https://docs.docker.com/build/building/multi-stage/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[04 - Docker and GitHub Actions/06 - Laravel Automated Testing\|Laravel Automated Testing]] | [[04 - Docker and GitHub Actions/08 - Container Cleanup\|Container Cleanup]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
