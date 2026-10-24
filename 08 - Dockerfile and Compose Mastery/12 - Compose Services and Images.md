---
title: "Compose Services and Images"
subject: Docker
level: fundamentals
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - fundamentals
---

# Compose Services and Images

Choose between existing images and source builds, and locate every requested Compose property.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Choose between existing images and source builds, and locate every requested Compose property.

## Questions answered

- What do Compose services mean and where does each important property belong?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/11 - Compose YAML Syntax Fundamentals|Compose YAML Syntax Fundamentals]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

A service is a logical runtime component, not an image layer. Multiple services may use the same image with different command or configuration. image names an artifact; build describes how to construct one. They may coexist so a source build gets a useful tag, but the documented pull policy determines behavior when Compose can both pull and build. Do not assume specifying build always prevents pulling an image.

Services are mappings under services. Other top-level resource mappings describe networks, volumes, secrets, or configs, and services attach to them. The directory below records scope, types, omitted behavior, an example, and limitations for every requested property.

## Syntax reference


| Choice | Use | Scenario |
| --- | --- | --- |
| image | Use an existing artifact | Run MariaDB or deploy a tested digest. |
| build | Construct from source | Iterate on a local PHP runtime. |
| image plus build | Build/tag with explicit source policy | Use pull_policy: build for deliberate local construction. |


## Code examples

**Complete image-only compose.yml:**
```yaml
name: mastery-services
services:
  identify:
    image: alpine:3.22
    command: ["cat", "/etc/os-release"]
```

**Incorrect model fragment:**
```yaml
services:
  identify:
    context: .
```

context belongs under build, not directly under a service.

## Line-by-line walkthrough

1. name establishes project identity.

2. services contains a service ID named identify.

3. image supplies its filesystem and executables.

4. command replaces the image CMD with a short diagnostic process.

5. The service exits normally; not every service is an HTTP daemon.

6. Read the property directory by scope before copying a nested setting.

## DO ✅ / DON'T ❌

- **DO ✅** Choose image/build policy explicitly when both appear, because artifact source affects reproducibility.
- **DO ✅** Keep role names descriptive, because service names also become network DNS names.

- **DON'T ❌** Put build-only keys directly under a service, because valid YAML can still violate Compose schema.
- **DON'T ❌** Assume a label or expose entry establishes authorization, because metadata is not access control.

## Expected result

Your plan uses existing database images and an explicit source policy for custom application images. This is an expected result, not an execution claim.

## Common errors and troubleshooting

“Additional property context is not allowed” indicates wrong nesting. Pull denied can arise before the expected build when image/build policy is unclear. Never add registry write permission merely to run a public image.

## Practice exercise

**Problem and objective:** Design service entries for PHP app, nginx frontend, and MariaDB, identifying which need build versus image.

**Success condition:** Your plan uses existing database images and an explicit source policy for custom application images.

**Hint:** Read the selected base and service artifact roles separately.

## Knowledge check

1. Is a service an image layer?
2. Where is context nested?
3. Can two services share an image?
4. Does build always prohibit pulling?
5. Does read_only make every mounted volume read-only?

> [!question]- Answers
> 1. No.
> 2. Under build.
> 3. Yes.
> 4. No.
> 5. No.

## Property directory

Dotted names in prose identify location; they are not literal flat YAML keys. Values below are reference fragments, not one combined deployable stack. Top-level resources need both declarations and service attachments. Engine, driver, platform, and Compose version can affect defaults; inspect the real resolved model and runtime.

### name

**Location:** Top-level. **Accepted value:** String.

**Omitted/default behavior:** Project identity otherwise follows CLI -p, COMPOSE_PROJECT_NAME, then name/directory precedence.

**Use, limits, and common mistakes:** Namespace resources deliberately; avoid one fixed name for concurrent independent runs.

**Syntax example (fragment):**
```yaml
name: mastery-lab
```

### services

**Location:** Top-level. **Accepted value:** Mapping of service IDs to configurations.

**Omitted/default behavior:** No useful runnable application without service definitions.

**Use, limits, and common mistakes:** Declare logical components, not a list of container IDs.

**Syntax example (fragment):**
```yaml
services: {tool: {image: "alpine:3.22"}}
```

### image

**Location:** Service. **Accepted value:** Image reference string.

**Omitted/default behavior:** May be absent when building; no universal default image.

**Use, limits, and common mistakes:** Use existing artifacts; a tag is mutable and not a content guarantee.

**Syntax example (fragment):**
```yaml
image: nginx:stable-alpine
```

### build

**Location:** Service. **Accepted value:** Context string or build mapping.

**Omitted/default behavior:** No source build if omitted.

**Use, limits, and common mistakes:** Choose build inputs; with image too, pull_policy affects pull versus build.

**Syntax example (fragment):**
```yaml
build: {context: ., dockerfile: Dockerfile}
```

### context

**Location:** Nested under build. **Accepted value:** Path or supported remote context string.

**Omitted/default behavior:** Build mapping context defaults to project directory (.).

**Use, limits, and common mistakes:** COPY reads this boundary; recipe location is not context root.

**Syntax example (fragment):**
```yaml
context: ./backend
```

### dockerfile

**Location:** Nested under build. **Accepted value:** Path string relative to context.

**Omitted/default behavior:** Dockerfile in context.

**Use, limits, and common mistakes:** Select a recipe; it does not broaden the context.

**Syntax example (fragment):**
```yaml
dockerfile: Dockerfile.dev
```

### ports

**Location:** Service. **Accepted value:** List of short strings or long mapping entries.

**Omitted/default behavior:** No host publication; omitted host IP in a mapping usually binds all interfaces.

**Use, limits, and common mistakes:** Publish only intended access; internal peers use container port 80.

**Syntax example (fragment):**
```yaml
ports: ["127.0.0.1:8080:80"]
```

### expose

**Location:** Service. **Accepted value:** List of container-port values.

**Omitted/default behavior:** No extra declarations; it is not a network firewall.

**Use, limits, and common mistakes:** Document internal listeners; absence does not isolate peers on a shared network.

**Syntax example (fragment):**
```yaml
expose: ["9000"]
```

### volumes

**Location:** Top-level declaration and service mount list. **Accepted value:** Top-level mapping; service list of short strings or long mount mappings.

**Omitted/default behavior:** No extra mounts; image-declared volumes can still exist.

**Use, limits, and common mistakes:** Declare named data separately at top level; host paths mean bind mounts.

**Syntax example (fragment):**
```yaml
volumes: ["db-data:/var/lib/mysql"]
```

### networks

**Location:** Top-level declaration and service attachments. **Accepted value:** Top-level mapping; service list or mapping.

**Omitted/default behavior:** Services normally join the implicit project default network.

**Use, limits, and common mistakes:** Attach only necessary peers; a name alone does not set internal: true.

**Syntax example (fragment):**
```yaml
networks: [backend]
```

### environment

**Location:** Service. **Accepted value:** Mapping or list of KEY=value strings.

**Omitted/default behavior:** Image ENV remains unless overridden.

**Use, limits, and common mistakes:** Runtime strings; quote booleans/numbers. A key with no value is not always the same as an empty string.

**Syntax example (fragment):**
```yaml
environment: {APP_MODE: local}
```

### env_file

**Location:** Service. **Accepted value:** Path/list or supported long-form file mappings.

**Omitted/default behavior:** None; interpolation .env does not automatically become a container env_file.

**Use, limits, and common mistakes:** Populate container environment; explicit environment values take precedence, even empty values.

**Syntax example (fragment):**
```yaml
env_file: [./runtime.env]
```

### depends_on

**Location:** Service. **Accepted value:** List or mapping to dependency options.

**Omitted/default behavior:** No explicit dependency sequencing.

**Use, limits, and common mistakes:** Short form waits for started, not necessarily ready; later failure is not continuous supervision.

**Syntax example (fragment):**
```yaml
depends_on: {db: {condition: service_healthy}}
```

### healthcheck

**Location:** Service. **Accepted value:** Mapping: test, timing, retries, disable.

**Omitted/default behavior:** Image check is inherited if present; otherwise none.

**Use, limits, and common mistakes:** Test availability with an installed executable; start_interval and related options require compatible tooling.

**Syntax example (fragment):**
```yaml
healthcheck: {test: ["CMD", "true"], interval: 10s}
```

### restart

**Location:** Service. **Accepted value:** Policy string: no, always, on-failure[:N], unless-stopped.

**Omitted/default behavior:** No automatic restart by default.

**Use, limits, and common mistakes:** Responds to process exits and daemon policy; unhealthy status alone does not trigger it. Quote "no".

**Syntax example (fragment):**
```yaml
restart: unless-stopped
```

### command

**Location:** Service. **Accepted value:** String, list, null or empty value.

**Omitted/default behavior:** null/omitted inherits image CMD; [] or empty string clears it.

**Use, limits, and common mistakes:** Overrides CMD; shell features require an explicit shell. It is not a Dockerfile RUN.

**Syntax example (fragment):**
```yaml
command: ["php", "artisan", "queue:work"]
```

### entrypoint

**Location:** Service. **Accepted value:** String, list, null or empty value.

**Omitted/default behavior:** null/omitted inherits image entrypoint; empty clears it.

**Use, limits, and common mistakes:** A non-null override ignores image default CMD; supply the complete intended startup.

**Syntax example (fragment):**
```yaml
entrypoint: ["php"]
```

### working_dir

**Location:** Service. **Accepted value:** Container path string.

**Omitted/default behavior:** Image WORKDIR or runtime default.

**Use, limits, and common mistakes:** Overrides runtime directory only; it does not change build COPY paths.

**Syntax example (fragment):**
```yaml
working_dir: /app
```

### user

**Location:** Service. **Accepted value:** User/UID optionally with group/GID string.

**Omitted/default behavior:** Image USER or runtime default, often root.

**Use, limits, and common mistakes:** Set runtime identity; mounted files must permit access. It does not create the account.

**Syntax example (fragment):**
```yaml
user: "10001:10001"
```

### profiles

**Location:** Service. **Accepted value:** List of profile-name strings.

**Omitted/default behavior:** No profiles means service is normally enabled.

**Use, limits, and common mistakes:** Enable optional tools with --profile; explicitly targeted profiled services may run without enabling the whole profile.

**Syntax example (fragment):**
```yaml
profiles: [tools]
```

### secrets

**Location:** Top-level declaration and per-service grants. **Accepted value:** Top-level mapping of sources; service list of names or grant mappings.

**Omitted/default behavior:** No extra grants.

**Use, limits, and common mistakes:** Mount secret files to selected services; local file secrets are not automatically encrypted at rest.

**Syntax example (fragment):**
```yaml
secrets: [db_password]
```

### configs

**Location:** Top-level declaration and per-service grants. **Accepted value:** Top-level mapping; service short or long grant list.

**Omitted/default behavior:** No extra grants.

**Use, limits, and common mistakes:** Use non-sensitive config files; not a secret channel. File-backed uid/gid/mode behavior can be implementation-limited.

**Syntax example (fragment):**
```yaml
configs: [{source: web_config, target: /etc/nginx/conf.d/default.conf}]
```

### logging

**Location:** Service. **Accepted value:** Mapping with driver and options.

**Omitted/default behavior:** Engine logging driver/default options.

**Use, limits, and common mistakes:** Bound logs for the selected driver; driver options are strings and retention can erase diagnostic evidence.

**Syntax example (fragment):**
```yaml
logging: {driver: json-file, options: {max-size: "10m", max-file: "3"}}
```

### deploy

**Location:** Service. **Accepted value:** Mapping from optional Deploy Specification.

**Omitted/default behavior:** Backend/implementation defaults.

**Use, limits, and common mistakes:** Do not assume Swarm placement/update/replica features behave identically in local Compose; verify actual enforcement.

**Syntax example (fragment):**
```yaml
deploy: {resources: {limits: {memory: 512M}}}
```

### extra_hosts

**Location:** Service. **Accepted value:** Host mappings in list or supported mapping form.

**Omitted/default behavior:** No extra hosts entries.

**Use, limits, and common mistakes:** Use deliberate host access where supported; it differs from Docker service DNS and expands host reachability.

**Syntax example (fragment):**
```yaml
extra_hosts: ["host.docker.internal:host-gateway"]
```

### dns

**Location:** Service. **Accepted value:** DNS server string or list.

**Omitted/default behavior:** Engine/network resolver behavior.

**Use, limits, and common mistakes:** Example-only documentation address: use an authorized real resolver in an actual config. Custom DNS is not a fix for wrong service names.

**Syntax example (fragment):**
```yaml
dns: ["192.0.2.53"]
```

### labels

**Location:** Service. **Accepted value:** Mapping or KEY=value list.

**Omitted/default behavior:** No extra service-defined labels.

**Use, limits, and common mistakes:** Container metadata may drive proxy automation; avoid secrets or reserved Compose label namespaces.

**Syntax example (fragment):**
```yaml
labels: {com.example.role: api}
```

### pull_policy

**Location:** Service. **Accepted value:** Supported policy string such as always, never, missing, build, or supported interval policy.

**Omitted/default behavior:** Usually missing; image+build has documented pull/build rules and latest is pulled even with missing.

**Use, limits, and common mistakes:** Choose artifact source deliberately; version-dependent policies need compatible Compose. never requires an available local image.

**Syntax example (fragment):**
```yaml
pull_policy: build
```

### init

**Location:** Service. **Accepted value:** Boolean.

**Omitted/default behavior:** False unless explicitly enabled.

**Use, limits, and common mistakes:** Use a small init to forward signals/reap children where supported; it does not supervise multiple applications.

**Syntax example (fragment):**
```yaml
init: true
```

### read_only

**Location:** Service. **Accepted value:** Boolean.

**Omitted/default behavior:** False for container root filesystem.

**Use, limits, and common mistakes:** Provide required writable mounts/tmpfs; it does not make all mounted volumes read-only.

**Syntax example (fragment):**
```yaml
read_only: true
```



## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Additional default and source details

When both image and build are configured and pull_policy is absent, Compose follows documented pull-first/build-fallback behavior rather than assuming source always wins. Use pull_policy: build for intentional source construction, or a selected image-only artifact for promotion. missing normally reuses available images, but latest has special pull behavior; never infer immutable content from that tag.

A healthcheck mapping inherits image defaults when not replaced. Underlying Engine probe defaults normally include 30s interval/timeout, 3 retries and no startup grace; supported start-interval defaults to 5s. Choose explicit measured timing for automation. A test list starts with CMD, CMD-SHELL or NONE; a plain string is a shell-style probe. Disable deliberately when overriding an inherited check.

Top-level configs can reference file, external resources, and supported content/environment sources depending on Compose version/backend. Top-level secrets can use file or Compose-supported environment sources and external references where supported. external: true means the resource must already exist in the target platform; it does not create an encrypted secret store. Services still need grants. Read the selected implementation’s uid/gid/mode limitations for file-backed sources.

Project names have character restrictions and precedence: CLI -p wins over COMPOSE_PROJECT_NAME, which wins over top-level name, then directory-derived defaults. Names should use supported lowercase letters/digits and separators. Do not force a shared fixed name across concurrent disposable runs.

compose.yml is a supported default filename used consistently in this module. The older notes use compose.yaml, also supported. Avoid placing both with conflicting content in one lab and expecting them to merge automatically; use one intentional base and explicit -f overlays.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/11 - Compose YAML Syntax Fundamentals|Compose YAML Syntax Fundamentals]]
- [[08 - Dockerfile and Compose Mastery/13 - Compose Build and Dockerfile Integration|Compose Build and Dockerfile Integration]]

## Official references

- [Compose documentation](https://docs.docker.com/reference/compose-file/)
- [Services documentation](https://docs.docker.com/reference/compose-file/services/)
- [Build documentation](https://docs.docker.com/reference/compose-file/build/)
- [Deploy documentation](https://docs.docker.com/reference/compose-file/deploy/)
- [Secrets documentation](https://docs.docker.com/reference/compose-file/secrets/)
- [Configs documentation](https://docs.docker.com/reference/compose-file/configs/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/11 - Compose YAML Syntax Fundamentals\|Compose YAML Syntax Fundamentals]] | [[08 - Dockerfile and Compose Mastery/13 - Compose Build and Dockerfile Integration\|Compose Build and Dockerfile Integration]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
