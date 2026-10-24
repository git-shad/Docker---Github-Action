---
title: "Container Registry Authentication"
subject: "GitHub Actions"
level: advanced
tags:
  - github-actions
  - advanced
---

# Container Registry Authentication

Authenticate locally without putting a token into command arguments.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Authenticate locally without putting a token into command arguments. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- Which credentials should CI and servers use to access GHCR?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[05 - Advanced CI CD/05 - Deployment Approvals|Deployment Approvals]].

## Introduction, purpose, and how it works

Authentication proves identity; authorization decides which package operations that identity may perform. GitHub Actions can publish using its short-lived built-in GITHUB_TOKEN with job-specific package permissions. A server outside Actions needs its own authorized registry identity. GHCR documents personal access token classic authentication for supported external use; grant only read:packages for a pull-only account and satisfy organization SSO policy when required. Token creation and storage need an operator-controlled lifecycle.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `--password-stdin` | Read a token from stdin | Use to avoid exposing it in process arguments. |
| `read -s` | Read without terminal echo in Bash | Use interactively; it is not encryption. |
| `docker logout ghcr.io` | Remove local login state | Use after temporary access; check credential helper behavior. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```bash
read -r -p "GHCR username: " GHCR_USER
read -r -s -p "Read-only GHCR token: " GHCR_TOKEN
printf "\n"
printf '%s' "$GHCR_TOKEN" | docker login ghcr.io -u "$GHCR_USER" --password-stdin
unset GHCR_TOKEN
docker logout ghcr.io
```

## Step-by-step walkthrough

1. Use Ubuntu Bash interactively on a trusted machine. Obtain an authorized least-privilege token through GitHub settings; do not paste it into the vault or shell history. Public images can often be pulled without login.

2. read -s hides terminal echo. The printf pipeline supplies the secret on standard input instead of a CLI argument. Avoid set -x or debug logging.

3. A successful login does not prove access to every package; test an explicitly authorized pull separately if needed.

4. Docker may store login material in its configuration unless a credential helper is configured. Prefer an appropriate helper and protect the account; logout removes the registry login. Unsetting the shell variable alone does not remove Docker’s stored login.

## Expected result

The machine authenticates, then removes the local registry login. No image is pushed or deployment changed.

## Practice

Compare a workflow’s contents: read and packages: write permissions with a server’s read-only token. Success: explain why the server should not receive the publishing credential.

## Common errors and troubleshooting

Denied with valid login: inspect package authorization and SSO. Credentials persisted unexpectedly: review Docker config and credential helper. Rotate leaked tokens; do not merely remove the visible text.

## Knowledge check

1. Are auth and authorization identical?
2. What should a pull server need?
3. What should Actions prefer?
4. Does unset log out Docker?
5. Should tokens appear in command arguments?

> [!question]- Answers
> 1. No.
> 2. Read-only package access.
> 3. Its scoped built-in token where supported.
> 4. No.
> 5. No.

## Cleanup

The example logs out and unsets the variable. Revoke a temporary lab token in GitHub when finished.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[05 - Advanced CI CD/05 - Deployment Approvals|Deployment Approvals]]
- [[05 - Advanced CI CD/07 - Deployment Rollback|Next in the roadmap: Deployment Rollback]]

## Official references

- [Registry official documentation](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)
- [Secrets official documentation](https://docs.github.com/en/actions/how-tos/write-workflows/choose-what-workflows-do/use-secrets)
- [Security official documentation](https://docs.github.com/en/actions/reference/security/secure-use)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[05 - Advanced CI CD/05 - Deployment Approvals\|Deployment Approvals]] | [[05 - Advanced CI CD/07 - Deployment Rollback\|Deployment Rollback]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
