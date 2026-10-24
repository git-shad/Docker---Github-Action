---
title: "Compose YAML Syntax Fundamentals"
subject: Docker
level: fundamentals
verified_on: 2026-10-21
tags:
  - docker
  - mastery
  - fundamentals
---

# Compose YAML Syntax Fundamentals

Write valid YAML and distinguish YAML parsing from Compose model validation.

> [!info] Documentation-only module
> Official references were checked on 2026-10-21. No Docker command, installation, application build, workflow, or deployment was executed. Expected outputs are illustrative. Complete runnable examples and intentionally incorrect fragments are labeled separately.

## Learning objectives

Write valid YAML and distinguish YAML parsing from Compose model validation.

## Questions answered

- How do indentation, data types, anchors, and extension fields work in compose.yml?

## Prerequisites and execution context

Examples use Ubuntu Linux and Bash with Docker Engine, BuildKit, and the Compose v2 plugin (docker compose). No installation is performed by these notes. Use WSL2 Ubuntu for Bash on Windows; these are not PowerShell commands. Docker Desktop users on macOS must account for Linux VM paths, permissions, and architecture. Readable image tags here are not immutable release pins; review supported versions and use recorded digests for release reproducibility.

Read first: [[08 - Dockerfile and Compose Mastery/01 - Dockerfile Syntax Fundamentals|Dockerfile Syntax Fundamentals]].

Unless identified as a reference fragment, illustrated files belong in a new disposable lab directory when you choose to practice. This vault stores those files only as Markdown examples. Existing-application exercises name their additional prerequisites explicitly.

## Explanation

YAML maps keys to values, and indentation makes nested mappings. A dash introduces a sequence item. Use spaces rather than tabs and keep sibling keys aligned. Strings, numbers, booleans and null have different types; quote environment values and port mappings when ambiguity would be harmful. A # begins a comment outside a quoted scalar; quote values containing a colon followed by space or a meaningful #.

Anchors assign a name with &, aliases reuse it with *, and mapping merge with << reuses mapping keys. An x- extension field holds reusable configuration that Compose does not treat as an ordinary service/resource by itself. Alias expansion happens before Compose interpolation. Mapping merges do not concatenate arbitrary lists, and redefining a nested mapping can replace inherited nested content. Duplicate keys should be rejected, not relied upon.

## Syntax reference

Mappings use key: value, sequences use - value, and | preserves a multiline string. All services must live beneath services. No obsolete version: field is needed. YAML syntax validity does not guarantee Compose schema validity.

## Code examples

**Complete compose.yml:**
```yaml
name: mastery-yaml
x-defaults: &defaults
  image: alpine:3.22
  restart: "no"
services:
  report:
    <<: *defaults
    environment:
      FEATURE_ENABLED: "false"
      MESSAGE: "hello: learner"
    command:
      - sh
      - -c
      - |
        printf "%s\n" "$$MESSAGE"
```

**Incorrect hierarchy fragment:**
```yaml
services:
  report:
  image: alpine:3.22
```

image is accidentally a sibling service key rather than a property of report. Indent it two more spaces.

**Optional validation from the lab directory:**
```bash
docker compose -f compose.yml config --quiet
```


## Line-by-line walkthrough

1. name chooses an explicit project identity; use a unique name for simultaneous labs.

2. x-defaults names a reusable mapping with an anchor. It does not start a container.

3. report merges that mapping, then adds its own environment and command.

4. Quoted false stays a string appropriate for process environment.

5. The command list explicitly selects sh; the block scalar keeps the newline. $$ defers MESSAGE expansion until the container shell rather than Compose.

6. config --quiet checks the resolved model without starting services; missing referenced files or required variables can still cause failure.

## DO ✅ / DON'T ❌

- **DO ✅** Use consistent two-space indentation, because hierarchy changes meaning.
- **DO ✅** Inspect the resolved model, because successful YAML parsing is only the first layer.

- **DON'T ❌** Use anchors as a substitute for understandable design, because deep merges can obscure actual values.
- **DON'T ❌** Assume an unquoted value has the intended type, because parser rules can differ from intuition.

## Expected result

Both services remain mappings with string values and explicit commands. This is an expected result, not an execution claim.

## Common errors and troubleshooting

Tabs and misplaced keys can cause parse or schema failures. Undefined aliases fail YAML processing. An older generic YAML 1.1 parser may treat no/on differently from modern schema expectations; quote ambiguous strings.

## Practice exercise

**Problem and objective:** Add a second service reusing the defaults and its own MESSAGE.

**Success condition:** Both services remain mappings with string values and explicit commands.

**Hint:** Use a new service key aligned with report, not nested within it.

## Knowledge check

1. What defines hierarchy?
2. Are tabs safe indentation?
3. What does & name?
4. Does x-defaults start a service?
5. Does parsing prove schema correctness?

> [!question]- Answers
> 1. Indentation.
> 2. No.
> 3. An anchor.
> 4. No.
> 5. No.

## Cleanup

Reading and editing the Markdown examples creates no Docker resources. If you later run a lab, remove only its named containers/images or its explicitly selected Compose project. docker compose down removes project containers and networks while preserving named volumes; adding --volumes destroys project volume data and belongs only in a confirmed disposable lab. Preserve needed diagnostics before removal.

## Related lessons

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery Roadmap]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[08 - Dockerfile and Compose Mastery/01 - Dockerfile Syntax Fundamentals|Dockerfile Syntax Fundamentals]]
- [[08 - Dockerfile and Compose Mastery/12 - Compose Services and Images|Compose Services and Images]]

## Official references

- [Compose documentation](https://docs.docker.com/reference/compose-file/)
- [Fragment documentation](https://docs.docker.com/reference/compose-file/fragments/)
- [Env documentation](https://docs.docker.com/compose/how-tos/environment-variables/variable-interpolation/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/10 - Dockerfile Anti-patterns\|Dockerfile Anti-patterns]] | [[08 - Dockerfile and Compose Mastery/12 - Compose Services and Images\|Compose Services and Images]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
