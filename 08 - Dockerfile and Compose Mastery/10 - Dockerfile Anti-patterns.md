---
title: "Dockerfile Anti-patterns"
subject: Docker
level: advanced
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - advanced
---

# Dockerfile Anti-patterns

Repair lifecycle and input mistakes instead of hiding their symptoms.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Repair lifecycle and input mistakes instead of hiding their symptoms.

## Questions answered

- Which Dockerfile anti-patterns should I recognize?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/09 - Dockerfile Best Practices|Dockerfile Best Practices]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

An anti-pattern can parse successfully while violating intent. A server in RUN belongs to the wrong lifecycle. Copying a secret then deleting it in a later layer leaves earlier content recoverable. Failure masking can produce a green image build after a required operation failed. A broad COPY can include private settings and incompatible host dependencies.

The correction should address the boundary: startup metadata for servers, controlled context for source, secret mounts for build access, and meaningful exit codes for gates. If a real credential leaked into an artifact or log, rotate it and handle exposed artifacts; merely editing the recipe is insufficient.

## Syntax reference


| Mistake | Repair | Why |
| --- | --- | --- |
| RUN npm run dev | Startup CMD for a dev server | Construction must finish. |
| COPY .env then delete later | Runtime secret delivery | Earlier layers remain recoverable. |
| Separate apt update/install steps | One related update/install/cleanup step | Avoid stale indexes. |
| COPY all before install | Copy manifests before code | Reuse dependency install appropriately. |
| required-command ; echo done | Failure-sensitive chaining | Preserve nonzero results. |


## Code examples

**Incorrect review fragment:**
```dockerfile
RUN false ; printf "looks successful\n"
```

The last successful command hides the initial failure.

**Correct full gating demonstration:**
```dockerfile
FROM alpine:3.22
RUN test -r /etc/os-release && printf "required input exists\n"
CMD ["cat", "/etc/os-release"]
```


## Line-by-line walkthrough

1. The bad fragment returns the final printf status.

2. The corrected base supplies shell builtins and cat.

3. The && chain runs the dependent operation only after the required test succeeds.

4. CMD remains a separate runtime operation.

5. Pipelines have additional status rules: use an explicitly available shell and supported pipefail options when needed, not an assumption that all sh implementations equal Bash.

## DO ✅ / DON'T ❌

- **DO ✅** Preserve a required failure status, because a printed success line proves nothing.
- **DO ✅** Explain why each repair works, because memorized style rules are easy to misuse.

- **DON'T ❌** Demonstrate credential leakage with real secrets, because the demonstration itself would distribute them.
- **DON'T ❌** Silence required errors with || true, because later steps could publish broken output.

## Expected result

Your corrections separately address versioning, context, startup, and identity. This is an expected result, not an execution claim.

## Common errors and troubleshooting

A hanging build may contain a foreground service. A false success may come from shell chaining. Trace the earliest failed requirement and resulting exit code.

## Practice exercise

**Problem and objective:** Repair a recipe using latest, broad COPY, a server RUN, and root runtime.

**Success condition:** Your corrections separately address versioning, context, startup, and identity.

**Hint:** A shorter recipe is not necessarily a safer one.

## Knowledge check

1. Does later deletion erase earlier layers?
2. Can ; hide an earlier failure?
3. Why not a server in RUN?
4. Is COPY all always wrong?
5. What follows a real leak?

> [!question]- Answers
> 1. No.
> 2. Yes, through the final command status.
> 3. It belongs at runtime.
> 4. No, but inputs and ignores must be reviewed.
> 5. Rotation and exposure remediation.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/09 - Dockerfile Best Practices|Dockerfile Best Practices]]
- [[08 - Dockerfile and Compose Mastery/11 - Compose YAML Syntax Fundamentals|Compose YAML Syntax Fundamentals]]

## Official references

- [Best documentation](https://docs.docker.com/build/building/best-practices/)
- [Secret documentation](https://docs.docker.com/build/building/secrets/)
- [Cache documentation](https://docs.docker.com/build/cache/optimize/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/09 - Dockerfile Best Practices\|Dockerfile Best Practices]] | [[08 - Dockerfile and Compose Mastery/11 - Compose YAML Syntax Fundamentals\|Compose YAML Syntax Fundamentals]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
