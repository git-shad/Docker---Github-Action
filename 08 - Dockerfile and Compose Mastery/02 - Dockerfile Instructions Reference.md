---
title: "Dockerfile Instructions Reference"
subject: Docker
level: intermediate
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - intermediate
---

# Dockerfile Instructions Reference

Use a detailed lifecycle reference for all 17 requested Dockerfile instructions.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Use a detailed lifecycle reference for all 17 requested Dockerfile instructions.

## Questions answered

- What exactly does each important Dockerfile instruction accept and change?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/01 - Dockerfile Syntax Fundamentals|Dockerfile Syntax Fundamentals]], [[08 - Dockerfile and Compose Mastery/03 - FROM RUN CMD and ENTRYPOINT|FROM RUN CMD and ENTRYPOINT]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

Instructions have different lifecycles. File-changing instructions build content; metadata instructions define later behavior; ONBUILD defers a trigger into a child build. Treating them all as shell commands is the central mistake this reference prevents. The cards below explicitly state what repetition, omission, and inherited configuration mean for each instruction.

## Syntax reference

The reference uses UPPERCASE keywords and schematic parameter names in prose. Only blocks explicitly identified as a complete example should be built directly. Modern frontend flags are feature-gated: check the selected frontend and Engine before relying on optional behavior.

## Code examples

**Complete lookup exercise:**
```dockerfile
FROM alpine:3.22
WORKDIR /lesson
RUN printf "reference\n" > message.txt
LABEL org.opencontainers.image.title="Instruction reference lab"
USER 10001:10001
CMD ["cat", "/lesson/message.txt"]
```


## Line-by-line walkthrough

1. FROM selects the base; WORKDIR creates/selects the path.

2. RUN creates a file during construction.

3. LABEL records an inspectable non-secret claim.

4. USER changes identity; the world-readable file still works for cat.

5. CMD defines the future process. Read each corresponding card below to explain repetition and omission.

## DO ✅ / DON'T ❌

- **DO ✅** Check both explicit declarations and base-image inheritance, because omitted metadata can still exist.
- **DO ✅** Match syntax to a real selected frontend, because not every current flag exists on older builders.

- **DON'T ❌** Use the reference fragments as complete applications, because they omit context deliberately.
- **DON'T ❌** Use deprecated MAINTAINER instead of interoperable LABEL metadata.

## Expected result

You identify inherited defaults or absence of a runnable command rather than assuming omission always means no effect. This is an expected result, not an execution claim.

## Common errors and troubleshooting

If behavior differs from the recipe, inspect the base metadata, selected stage, runtime overrides, and actual builder capabilities. A valid instruction can still have inappropriate lifecycle semantics.

## Practice exercise

**Problem and objective:** Annotate every line of a small recipe using the cards, then predict the result of removing CMD.

**Success condition:** You identify inherited defaults or absence of a runnable command rather than assuming omission always means no effect.

**Hint:** Base configuration participates in the result.

## Knowledge check

1. Are all instructions shell commands?
2. Do metadata omissions always erase base defaults?
3. Which instruction defers into a child build?
4. What replaces deprecated author metadata?
5. Can optional flags depend on frontend version?

> [!question]- Answers
> 1. No.
> 2. No.
> 3. ONBUILD.
> 4. LABEL.
> 5. Yes.

## Detailed instruction cards

Each card covers meaning, purpose, syntax/arguments, processing, phase, repetition, omission, limits, mistakes, recommendations, and interaction. Incorrect examples below are review fragments, not standalone Dockerfiles. Some are valid syntax with deliberately poor semantics.

### FROM

**What and why:** Choose a parent filesystem and begin a stage.

**Syntax and accepted arguments:** FROM [--platform=PLATFORM] IMAGE[:TAG|@DIGEST] [AS NAME]

**Processing:** The builder resolves the base and inherits its filesystem and image configuration. It does not start its application.

**Phase:** Build selection plus inherited runtime defaults.

**Multiple declarations:** Repeat to start multiple stages; each begins a new stage.

**If omitted:** A normal recipe needs FROM after any initial directives/global ARG. scratch is an explicit empty base, not an omitted base.

**When to use / DO ✅:** Use a supported runtime with a reviewed digest for reproducible releases.

**Limits:** A tag can move; scratch has no shell, libraries, certificates, or executables.

**Common mistake / DON’T ❌:** Avoid hardcoding a platform unless required; it can break multi-platform builds.

**Interactions:** Later RUN/COPY/USER operate in this stage; ARG before FROM can choose the base.

**Correct fragment:**
```dockerfile
FROM alpine:3.22
```

**Incorrect or inappropriate fragment:**
```dockerfile
FROM
```

**Why:** Missing image argument makes the instruction incomplete.

### RUN

**What and why:** Prepare filesystem content during construction.

**Syntax and accepted arguments:** RUN [OPTIONS] command; or RUN [OPTIONS] ["executable", "arg"]

**Processing:** A temporary build process executes; nonzero status fails the step unless the shell masks it. Filesystem changes can be cached.

**Phase:** Build execution; resulting files affect runtime.

**Multiple declarations:** Repeat for related preparation steps, balancing cache boundaries and clarity.

**If omitted:** No extra build commands run; the base and copied content must already suffice.

**When to use / DO ✅:** Use to install required packages, compile code, or make directories.

**Limits:** It cannot define a lasting runtime daemon; network outputs are not automatically refreshed on cache hits.

**Common mistake / DON’T ❌:** Avoid continuous servers and secret values. Use failure-sensitive chains.

**Interactions:** WORKDIR, USER, ENV, ARG, and SHELL affect execution; BuildKit mount options affect temporary inputs.

**Correct fragment:**
```dockerfile
RUN test -r /etc/os-release && printf "ready\n"
```

**Incorrect or inappropriate fragment:**
```dockerfile
RUN false ; echo "passed"
```

**Why:** The final echo can mask the required operation failure.

### CMD

**What and why:** Provide replaceable runtime defaults.

**Syntax and accepted arguments:** CMD ["executable", "arg"]; CMD ["arg"]; or CMD shell text

**Processing:** The builder stores metadata; container startup combines it with ENTRYPOINT or uses it as the command.

**Phase:** Runtime metadata written at build time.

**Multiple declarations:** Only the final effective CMD in a stage is used.

**If omitted:** Inherited CMD may remain; without a usable command or entrypoint startup has nothing to execute.

**When to use / DO ✅:** Use an application default command or arguments for a fixed entrypoint.

**Limits:** Exec form has no shell expansion. Runtime arguments replace it.

**Common mistake / DON’T ❌:** Avoid using several CMD lines as a process supervisor.

**Interactions:** Exec ENTRYPOINT receives CMD as arguments; new ENTRYPOINT can clear inherited CMD.

**Correct fragment:**
```dockerfile
CMD ["cat", "/etc/os-release"]
```

**Incorrect or inappropriate fragment:**
```dockerfile
CMD ['cat', '/etc/os-release']
```

**Why:** Single quotes are not JSON array string syntax.

### ENTRYPOINT

**What and why:** Define the primary executable for a tool-like image.

**Syntax and accepted arguments:** ENTRYPOINT ["executable", "arg"]; or ENTRYPOINT shell text

**Processing:** Stores startup executable metadata; exec form combines with CMD at container creation.

**Phase:** Runtime metadata.

**Multiple declarations:** Only the last effective ENTRYPOINT applies.

**If omitted:** The inherited entrypoint remains if present; otherwise CMD must specify an executable.

**When to use / DO ✅:** Use for a fixed command that accepts replaceable default arguments.

**Limits:** Shell form changes argument and signal behavior; runtime entrypoint overrides are possible.

**Common mistake / DON’T ❌:** Avoid wrappers that forget exec when handing off to long-lived applications.

**Interactions:** Pairs with CMD; USER and WORKDIR affect its eventual process.

**Correct fragment:**
```dockerfile
ENTRYPOINT ["php"]
```

**Incorrect or inappropriate fragment:**
```dockerfile
ENTRYPOINT ["php artisan"]
```

**Why:** The array names one executable containing a space instead of executable php plus arguments.

### WORKDIR

**What and why:** Set a consistent current directory.

**Syntax and accepted arguments:** WORKDIR /absolute/path

**Processing:** Creates/selects a directory for subsequent build operations and saved runtime working directory.

**Phase:** Both build and runtime.

**Multiple declarations:** Repeat; relative paths resolve from the previous working directory.

**If omitted:** The base directory/default working directory remains, which may not be what you expect.

**When to use / DO ✅:** Use an absolute application path before relative COPY or RUN.

**Limits:** It does not change the context root or fix ownership of mounted host files.

**Common mistake / DON’T ❌:** Avoid relying on an undocumented parent image directory.

**Interactions:** Controls RUN, CMD, ENTRYPOINT execution directory and relative COPY/ADD destinations.

**Correct fragment:**
```dockerfile
WORKDIR /app
```

**Incorrect or inappropriate fragment:**
```dockerfile
RUN cd /app
```

**Why:** A cd in one RUN does not persist as the next instruction working directory.

### COPY

**What and why:** Include known files with explicit copy semantics.

**Syntax and accepted arguments:** COPY [OPTIONS] SOURCE... DEST; or COPY [OPTIONS] ["SOURCE", "DEST"]

**Processing:** Reads allowed context inputs or a --from stage/image/context and adds files to the destination.

**Phase:** Build files used at runtime.

**Multiple declarations:** Repeat for cache and ownership boundaries.

**If omitted:** No local project source is automatically included.

**When to use / DO ✅:** Use for ordinary manifests, source, and cross-stage artifacts.

**Limits:** Cannot read ordinary sources outside context; ignored files are unavailable. --chown/--chmod are Linux-oriented options.

**Common mistake / DON’T ❌:** Avoid broad source inclusion without reviewed ignore rules.

**Interactions:** WORKDIR resolves relative destination; --from chooses a different source filesystem.

**Correct fragment:**
```dockerfile
COPY --chown=10001:10001 message.txt /app/message.txt
```

**Incorrect or inappropriate fragment:**
```dockerfile
COPY ../private.key /app/private.key
```

**Why:** The source is outside context and is sensitive material inappropriate for the image.

### ADD

**What and why:** Support additional source handling beyond ordinary COPY.

**Syntax and accepted arguments:** ADD [OPTIONS] SOURCE... DEST; or JSON-array source/destination form

**Processing:** Adds local files, can unpack recognized local tar content, and supports documented remote URL/Git sources.

**Phase:** Build files used at runtime.

**Multiple declarations:** Repeat when deliberate source transformations are needed.

**If omitted:** No effect; COPY is normally sufficient for local application files.

**When to use / DO ✅:** Use local archive extraction or reviewed remote-source features with integrity controls.

**Limits:** Remote archives and local archives do not have identical automatic extraction behavior. Feature flags depend on frontend version.

**Common mistake / DON’T ❌:** Avoid unverified remote downloads; prefer explicit checksums and trusted inputs.

**Interactions:** WORKDIR affects destination; COPY is the narrower alternative.

**Correct fragment:**
```dockerfile
ADD source.tar /app/
```

**Incorrect or inappropriate fragment:**
```dockerfile
ADD . /app/
```

**Why:** This can be syntactically valid but uses broader semantics unnecessarily for normal source inclusion.

### ENV

**What and why:** Store non-secret environment defaults.

**Syntax and accepted arguments:** ENV NAME=value [OTHER=value ...]

**Processing:** Sets image configuration and influences later supported expansion and build processes.

**Phase:** Both.

**Multiple declarations:** Repeat; later values replace earlier same-name values.

**If omitted:** Inherited environment remains; missing application variables need runtime configuration.

**When to use / DO ✅:** Use safe defaults that callers can override.

**Limits:** Values persist and are inspectable. Legacy whitespace form is ambiguous; use key=value syntax.

**Common mistake / DON’T ❌:** Avoid credentials and temporary build-only values.

**Interactions:** ENV overrides same-name ARG for relevant expansion; runtime environment may override ENV.

**Correct fragment:**
```dockerfile
ENV APP_MODE=local
```

**Incorrect or inappropriate fragment:**
```dockerfile
ENV API_TOKEN=dummy-example-value
```

**Why:** Even a dummy illustrates the wrong secret channel; real credentials would persist in image configuration.

### ARG

**What and why:** Parameterize non-secret build choices.

**Syntax and accepted arguments:** ARG NAME[=DEFAULT]

**Processing:** Declares an argument in scope and accepts build-supplied values; global and stage scope differ.

**Phase:** Build; values may appear in build metadata even without runtime ENV.

**Multiple declarations:** Repeat to redeclare in stage scope or change defaults deliberately.

**If omitted:** Undeclared ordinary build values are not available; automatic/global arguments have their own scope rules.

**When to use / DO ✅:** Use for versions or flags reviewed as non-sensitive.

**Limits:** Not a secret mechanism; scope and stage inheritance matter. Global automatic platform values need declaration for RUN use.

**Common mistake / DON’T ❌:** Avoid treating ARG as encrypted or ephemeral secrecy.

**Interactions:** Global ARG can parameterize FROM; ENV can copy a value into persistent runtime metadata.

**Correct fragment:**
```dockerfile
ARG APP_VARIANT=training
```

**Incorrect or inappropriate fragment:**
```dockerfile
ARG DATABASE_PASSWORD
```

**Why:** This chooses a build argument for sensitive data rather than a secret mount.

### EXPOSE

**What and why:** Document intended container listener ports.

**Syntax and accepted arguments:** EXPOSE PORT[/tcp|/udp] [MORE_PORTS...]

**Processing:** Stores image metadata; it does not create a listener or host mapping.

**Phase:** Runtime metadata.

**Multiple declarations:** Repeat to describe several ports/protocols.

**If omitted:** Applications can still listen and operators can still publish explicitly.

**When to use / DO ✅:** Use to document the port the process actually serves.

**Limits:** Not a firewall or permission boundary; default protocol is tcp.

**Common mistake / DON’T ❌:** Avoid describing EXPOSE as public access or security isolation.

**Interactions:** Compose ports or docker run -p publishes host access independently.

**Correct fragment:**
```dockerfile
EXPOSE 8000/tcp
```

**Incorrect or inappropriate fragment:**
```dockerfile
EXPOSE 127.0.0.1:8000:8000
```

**Why:** Host-binding syntax belongs to runtime port publishing, not EXPOSE.

### VOLUME

**What and why:** Declare intended externally mounted storage paths.

**Syntax and accepted arguments:** VOLUME ["/path", "/other"]; or VOLUME /path

**Processing:** Saves a mount destination; container creation may allocate anonymous volumes when no explicit mount is supplied.

**Phase:** Runtime storage metadata; builder behavior around later writes varies.

**Multiple declarations:** Repeat for distinct mount paths.

**If omitted:** Ordinary writable-layer paths remain unless the base declares volumes or runtime mounts are provided.

**When to use / DO ✅:** Use only when image-level storage intent is useful; choose operator-managed named mounts in Compose.

**Limits:** Cannot bind a host source. It does not provide backup, naming, or retention policy.

**Common mistake / DON’T ❌:** Avoid relying on anonymous volumes for important data identity.

**Interactions:** A runtime named/bind mount can supply the declared destination and hide image content there.

**Correct fragment:**
```dockerfile
VOLUME ["/data"]
```

**Incorrect or inappropriate fragment:**
```dockerfile
VOLUME ["/host/path:/data"]
```

**Why:** The value is a destination path, not runtime source:target syntax.

### USER

**What and why:** Select least-privilege identity.

**Syntax and accepted arguments:** USER USER[:GROUP]; or USER UID[:GID]

**Processing:** Changes the user for later RUN and for runtime startup.

**Phase:** Both.

**Multiple declarations:** Repeat when preparation needs controlled identity switches.

**If omitted:** The base identity remains; many bases default to root.

**When to use / DO ✅:** Use after root-only preparation and explicit writable-directory ownership.

**Limits:** Does not create users/groups, home directories, or host bind-mount permissions.

**Common mistake / DON’T ❌:** Avoid leaving root as a convenience fix for path permissions.

**Interactions:** WORKDIR and COPY ownership must permit the chosen user’s required reads/writes.

**Correct fragment:**
```dockerfile
USER 10001:10001
```

**Incorrect or inappropriate fragment:**
```dockerfile
USER 10001:10001
RUN apk add --no-cache curl
```

**Why:** The unprivileged identity normally cannot install OS packages.

### LABEL

**What and why:** Attach non-secret image metadata.

**Syntax and accepted arguments:** LABEL KEY=value [OTHER=value ...]

**Processing:** Writes key/value metadata such as title, source, or revision.

**Phase:** Image metadata consumed by tools.

**Multiple declarations:** Repeat; later same-key values replace earlier values.

**If omitted:** No extra labels beyond inherited metadata.

**When to use / DO ✅:** Use interoperable OCI keys and actual verified source/revision values.

**Limits:** A label is a claim, not cryptographic provenance or access control.

**Common mistake / DON’T ❌:** Avoid invented provenance or private values.

**Interactions:** ARG can supply reviewed revision values; labels do not modify application environment.

**Correct fragment:**
```dockerfile
LABEL org.opencontainers.image.title="Learning image"
```

**Incorrect or inappropriate fragment:**
```dockerfile
LABEL api.password="dummy-example-value"
```

**Why:** Metadata is inspectable and inappropriate for secrets.

### HEALTHCHECK

**What and why:** Observe application capability after startup.

**Syntax and accepted arguments:** HEALTHCHECK [--interval=TIME --timeout=TIME --start-period=TIME --start-interval=TIME --retries=N] CMD COMMAND; or HEALTHCHECK NONE

**Processing:** Stores a probe run inside the container; exit 0 means healthy, 1 unhealthy, 2 is reserved.

**Phase:** Runtime checks configured at build time.

**Multiple declarations:** Only the last effective health check applies; NONE disables an inherited check.

**If omitted:** No image-defined check unless inherited or supplied at runtime.

**When to use / DO ✅:** Use a bounded probe of a meaningful local capability.

**Limits:** The probe tool must exist. Unhealthy alone does not trigger an Engine restart. Some options require compatible Engine versions.

**Common mistake / DON’T ❌:** Avoid expensive probes, secret-bearing output, and claims that running equals ready.

**Interactions:** Compose healthcheck can override image settings; startup dependencies can consume the health state.

**Correct fragment:**
```dockerfile
HEALTHCHECK --interval=30s --timeout=3s CMD test -r /etc/os-release || exit 1
```

**Incorrect or inappropriate fragment:**
```dockerfile
HEALTHCHECK CMD curl http://127.0.0.1/
```

**Why:** If curl is absent or no HTTP listener exists, the probe fails independently of the intended application.

### SHELL

**What and why:** Choose the shell for later shell-form instructions.

**Syntax and accepted arguments:** SHELL ["executable", "parameter", ...]

**Processing:** Replaces the shell configuration used by supported shell-form RUN/CMD/ENTRYPOINT.

**Phase:** Both build and applicable runtime shell form.

**Multiple declarations:** Repeat; the most recent shell configuration governs later shell-form instructions.

**If omitted:** Linux normally inherits /bin/sh -c; Windows defaults differ.

**When to use / DO ✅:** Use when a deliberately installed shell or options are necessary.

**Limits:** Does not install Bash; exec-form commands still bypass it.

**Common mistake / DON’T ❌:** Avoid assuming Alpine or every minimal image contains Bash.

**Interactions:** Affects shell-form RUN/CMD/ENTRYPOINT, not ordinary COPY or exec-form argv.

**Correct fragment:**
```dockerfile
SHELL ["/bin/sh", "-c"]
```

**Incorrect or inappropriate fragment:**
```dockerfile
SHELL ["/bin/bash", "-o", "pipefail", "-c"]
```

**Why:** On an image without Bash this valid-looking configuration cannot execute; install/choose a suitable base first.

### STOPSIGNAL

**What and why:** Select the default graceful-stop signal.

**Syntax and accepted arguments:** STOPSIGNAL SIGNAL_NAME_OR_NUMBER

**Processing:** Records which signal Engine sends to the main container process when stopping it.

**Phase:** Runtime metadata.

**Multiple declarations:** Last effective value applies.

**If omitted:** Uses inherited setting or Engine default, normally SIGTERM.

**When to use / DO ✅:** Use only when the application documents a different graceful signal.

**Limits:** The process must handle it; a later timeout can still lead to forced termination.

**Common mistake / DON’T ❌:** Avoid choosing signals by guesswork or hiding broken forwarding with long timeouts.

**Interactions:** Exec startup and wrapper behavior determine which process receives the signal; runtime can override it.

**Correct fragment:**
```dockerfile
STOPSIGNAL SIGTERM
```

**Incorrect or inappropriate fragment:**
```dockerfile
STOPSIGNAL SIGKILL
```

**Why:** Immediate termination bypasses graceful cleanup and should not be the normal shutdown design.

### ONBUILD

**What and why:** Register a build trigger for downstream child images.

**Syntax and accepted arguments:** ONBUILD INSTRUCTION arguments

**Processing:** Stores a trigger that executes when this image is used as a parent in a later build, not immediately in this build.

**Phase:** Future child build.

**Multiple declarations:** Multiple triggers execute in registration order for the immediate child build and are not carried indefinitely.

**If omitted:** Child builds perform only their own instructions and inherited content.

**When to use / DO ✅:** Use only for explicitly documented template images where downstream context requirements are clear.

**Limits:** Cannot nest ONBUILD or use prohibited triggers such as FROM/MAINTAINER. Hidden child context assumptions are fragile.

**Common mistake / DON’T ❌:** Avoid surprising application images with undocumented downstream source-copy triggers.

**Interactions:** Runs after the child FROM; trigger COPY sources refer to the child context, not the parent build context.

**Correct fragment:**
```dockerfile
ONBUILD COPY . /app
```

**Incorrect or inappropriate fragment:**
```dockerfile
ONBUILD ONBUILD RUN echo nested
```

**Why:** Nested triggers are prohibited and obscure lifecycle intent.



## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Instruction options and inherited defaults


| Instruction family | Important accepted options and behavior |
| --- | --- |
| FROM | --platform accepts a platform expression such as linux/amd64 or an automatic build platform value. AS introduces an identifier used by later stages. Avoid fixing the target platform accidentally in multi-platform recipes. |
| RUN | --mount accepts type=bind/cache/tmpfs/secret/ssh plus type-specific fields; --network selects default/none/host where allowed. Privileged host network/security/device features require backend support and explicit entitlements. |
| COPY | --from chooses stage/image/named context; --chown accepts UID/GID or resolvable names; --chmod accepts supported permission syntax; --link isolates copied content for reuse with restrictions on destination symlink traversal. --parents/--exclude and other newer options require a frontend version supporting them. |
| ADD | Common ownership/mode/link options exist where supported. Remote-source controls such as --checksum (documented SHA-256 URL integrity) and --keep-git-dir apply to their intended source kinds; do not assume every option applies to every source. |
| HEALTHCHECK | CMD accepts a shell command or exec-form JSON argv; NONE disables inherited checking. Default Engine timings are normally 30s interval, 30s timeout, 3 retries, 0s start-period; start-interval normally defaults to 5s on supported Engines. Inspect explicit image and runtime overrides. |
| LABEL | Keys and values are strings; quote values containing whitespace. Inherited same-key values can be replaced. Labels are inspectable metadata. |
| SHELL | A JSON array of executable plus parameters, not a free-form shell string. It must name an actually installed executable. |

**Phase/scope reminder:** each stage starts from its selected base configuration. Global ARG values can parameterize FROM but need in-stage declarations for ordinary use. Automatic platform ARGs such as BUILDPLATFORM and TARGETPLATFORM describe builder/target choices; declaring them does not magically cross-compile native packages. Supported builder features must be checked against the selected frontend, Engine and build backend, not inferred only from a modern-looking Dockerfile.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/01 - Dockerfile Syntax Fundamentals|Dockerfile Syntax Fundamentals]]
- [[08 - Dockerfile and Compose Mastery/03 - FROM RUN CMD and ENTRYPOINT|FROM RUN CMD and ENTRYPOINT]]
- [[08 - Dockerfile and Compose Mastery/03 - FROM RUN CMD and ENTRYPOINT|FROM RUN CMD and ENTRYPOINT]]

## Official references

- [File documentation](https://docs.docker.com/reference/dockerfile/)
- [Best documentation](https://docs.docker.com/build/building/best-practices/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/01 - Dockerfile Syntax Fundamentals\|Dockerfile Syntax Fundamentals]] | [[08 - Dockerfile and Compose Mastery/03 - FROM RUN CMD and ENTRYPOINT\|FROM RUN CMD and ENTRYPOINT]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
