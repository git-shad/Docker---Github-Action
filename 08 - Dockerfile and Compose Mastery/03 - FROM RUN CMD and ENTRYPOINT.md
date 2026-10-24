---
title: "FROM RUN CMD and ENTRYPOINT"
subject: Docker
level: fundamentals
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - fundamentals
---

# FROM RUN CMD and ENTRYPOINT

Predict final argv, build behavior, and signal handling.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Predict final argv, build behavior, and signal handling.

## Questions answered

- How do FROM, RUN, CMD, and ENTRYPOINT interact?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/01 - Dockerfile Syntax Fundamentals|Dockerfile Syntax Fundamentals]], [[08 - Dockerfile and Compose Mastery/02 - Dockerfile Instructions Reference|Dockerfile Instructions Reference]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

FROM establishes a stage and inherited configuration. RUN executes a temporary build process. CMD and ENTRYPOINT store startup configuration. Exec-form ENTRYPOINT followed by CMD forms one argument vector. Runtime arguments replace CMD defaults, while an explicit entrypoint override replaces the fixed executable. Setting a new ENTRYPOINT can clear an inherited CMD, so define the intended pair explicitly.

Shell form normally wraps the command in /bin/sh -c. Exec form bypasses the shell and does not expand variables, pipes, wildcards, or &&. A wrapper must use exec to hand control to a long-running application so signals reach it. PID 1 also has child-reaping responsibilities; init support can help but does not fix an application that ignores shutdown.

## Syntax reference


| Instruction | Phase | Practical choice |
| --- | --- | --- |
| FROM | Select base during build; inherit metadata | Choose a maintained PHP runtime. |
| RUN | Execute during build | Install an extension or generate a file. |
| CMD | Default runtime command/arguments | Let users replace the default path. |
| ENTRYPOINT | Runtime executable | Make the image a fixed command-line tool. |


## Code examples

**Complete tool image:**
```dockerfile
FROM alpine:3.22
RUN printf "default\n" > /message.txt
ENTRYPOINT ["cat"]
CMD ["/message.txt"]
```

```bash
docker build -t mastery-cat .
docker run --rm mastery-cat
docker run --rm mastery-cat /etc/os-release
```

**Incorrect runtime intention:**
```dockerfile
RUN sleep 3600
```

This sleeps during construction. For runtime sleep use CMD ["sleep", "3600"] after FROM.

**Shell expansion fragment:**
```dockerfile
ENV MESSAGE=hello
CMD ["sh", "-c", "exec printf \"%s\\n\" \"$MESSAGE\""]
```


## Line-by-line walkthrough

1. FROM supplies cat.

2. RUN creates the default message at build time.

3. ENTRYPOINT fixes cat as the startup executable.

4. CMD supplies /message.txt when no alternate arguments are given.

5. The second run becomes cat /etc/os-release; it does not start a second process after cat.

6. The separate shell fragment expands MESSAGE at runtime, then exec replaces the shell. Plain ["echo", "$MESSAGE"] would pass a literal dollar expression.

## DO ✅ / DON'T ❌

- **DO ✅** Write final argv explicitly, because overrides combine metadata rather than execute two instructions sequentially.
- **DO ✅** Use direct startup and proper signal handling, because graceful shutdown depends on the process tree.

- **DON'T ❌** Assume /bin/sh supports every Bash option, because base distributions differ.
- **DON'T ❌** Use RUN for an intended continuously running service, because the build must finish.

## Expected result

Your answer identifies executable, default arguments, override behavior, and signal recipient. This is an expected result, not an execution claim.

## Common errors and troubleshooting

Executable not found can mean a whole shell expression was passed as one executable. A stop timeout may mean a shell wrapper never forwarded the signal. Inspect argv, process tree, and application shutdown behavior before changing timeouts.

## Practice exercise

**Problem and objective:** Predict the argv for both cat runs, then replace cat with a PHP CLI tool.

**Success condition:** Your answer identifies executable, default arguments, override behavior, and signal recipient.

**Hint:** Treat ENTRYPOINT and CMD as arrays joined at startup.

## Knowledge check

1. When is RUN executed?
2. What do runtime arguments replace?
3. Does exec form expand variables?
4. Why exec in a wrapper?
5. Do repeated CMD declarations accumulate?

> [!question]- Answers
> 1. During build.
> 2. CMD defaults.
> 3. No.
> 4. Replace the wrapper so the application receives signals directly.
> 5. No, only the last effective CMD applies.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/01 - Dockerfile Syntax Fundamentals|Dockerfile Syntax Fundamentals]]
- [[08 - Dockerfile and Compose Mastery/02 - Dockerfile Instructions Reference|Dockerfile Instructions Reference]]
- [[08 - Dockerfile and Compose Mastery/04 - COPY ADD WORKDIR and USER|COPY ADD WORKDIR and USER]]

## Official references

- [File documentation](https://docs.docker.com/reference/dockerfile/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/02 - Dockerfile Instructions Reference\|Dockerfile Instructions Reference]] | [[08 - Dockerfile and Compose Mastery/04 - COPY ADD WORKDIR and USER\|COPY ADD WORKDIR and USER]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
