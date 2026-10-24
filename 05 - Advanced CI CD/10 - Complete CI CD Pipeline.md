---
title: "Complete CI CD Pipeline"
subject: "GitHub Actions"
level: advanced
tags:
  - github-actions
  - advanced
---

# Complete CI CD Pipeline

Trace a complete single-host frontend release from commit to verified server response.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Trace a complete single-host frontend release from commit to verified server response. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I connect type validation, image publishing, approval, and server deployment?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[02 - Docker Development/07 - Multi-stage Docker Builds|Multi-stage Docker Builds]], [[05 - Advanced CI CD/01 - Deployment to Ubuntu Server|Deployment to Ubuntu Server]], [[05 - Advanced CI CD/05 - Deployment Approvals|Deployment Approvals]], [[05 - Advanced CI CD/07 - Deployment Rollback|Deployment Rollback]], [[04 - Docker and GitHub Actions/07 - React TypeScript Build Validation|React TypeScript Build Validation]].

## Introduction, purpose, and how it works

This capstone combines earlier lessons for an existing React TypeScript/Vite application. It performs real publishing and deployment if copied into a configured repository and triggered. It is a teaching pipeline for an authorized single-host target, with possible downtime; it is not a complete production platform or a full Laravel backend deployment. Tests must validate meaningful behavior, and a human approval must inspect the exact digest promoted to the server.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `jobs.build.outputs` | Expose the published digest | Use a step output, not a rebuilt tag guess. |
| `permissions: {}` | Remove unnecessary token scopes | Use for SSH-only deployment jobs. |
| `StrictHostKeyChecking=yes` | Reject unknown or mismatched server identity | Use a previously verified known_hosts entry. |
| `GITHUB_OUTPUT` | Record a step output | Use a valid one-line key/value, then expose it as a job output. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** CI demonstration in a practice repository. The walkthrough identifies filenames and the machine where each operation belongs.

```yaml
name: Frontend release
on:
  push:
    branches: [main]
permissions:
  contents: read
concurrency:
  group: frontend-production
  cancel-in-progress: false
jobs:
  build:
    runs-on: ubuntu-24.04
    timeout-minutes: 20
    permissions:
      contents: read
      packages: write
    outputs:
      image: ${{ steps.publish.outputs.image }}
    steps:
      - uses: actions/checkout@11bd71901bbe5b1630ceea73d27597364c9af683 # v4.2.2
        with:
          persist-credentials: false
      - name: Build with type validation
        run: |
          image="ghcr.io/${GITHUB_REPOSITORY,,}"
          docker build -t "$image:$GITHUB_SHA" .
      - name: Start candidate
        run: |
          image="ghcr.io/${GITHUB_REPOSITORY,,}"
          docker run -d --name candidate -p 127.0.0.1:8080:80 "$image:$GITHUB_SHA"
      - name: HTTP smoke test
        run: curl --fail --silent --show-error --retry 12 --retry-delay 2 --retry-connrefused http://127.0.0.1:8080/ -o /dev/null
      - name: Publish tested candidate
        id: publish
        env:
          GHCR_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        run: |
          image="ghcr.io/${GITHUB_REPOSITORY,,}"
          printf '%s' "$GHCR_TOKEN" | docker login ghcr.io -u "$GITHUB_ACTOR" --password-stdin
          docker push "$image:$GITHUB_SHA"
          ref="$(docker image inspect --format '{{index .RepoDigests 0}}' "$image:$GITHUB_SHA")"
          test -n "$ref"
          printf 'image=%s\n' "$ref" >> "$GITHUB_OUTPUT"
      - name: Cleanup candidate
        if: always()
        run: |
          if docker container inspect candidate >/dev/null 2>&1; then docker rm -f candidate; fi
          docker logout ghcr.io
  deploy:
    needs: build
    runs-on: ubuntu-24.04
    timeout-minutes: 10
    environment: production
    permissions: {}
    env:
      IMAGE_REF: ${{ needs.build.outputs.image }}
      DEPLOY_HOST: ${{ vars.DEPLOY_HOST }}
      DEPLOY_USER: ${{ vars.DEPLOY_USER }}
      DEPLOY_KEY: ${{ secrets.DEPLOY_KEY }}
      DEPLOY_KNOWN_HOSTS: ${{ secrets.DEPLOY_KNOWN_HOSTS }}
    steps:
      - name: Configure verified SSH identity
        run: |
          test -n "$DEPLOY_HOST" && test -n "$DEPLOY_USER"
          test -n "$DEPLOY_KEY" && test -n "$DEPLOY_KNOWN_HOSTS"
          install -d -m 700 "$HOME/.ssh"
          printf '%s\n' "$DEPLOY_KEY" > "$HOME/.ssh/id_ed25519"
          printf '%s\n' "$DEPLOY_KNOWN_HOSTS" > "$HOME/.ssh/known_hosts"
          chmod 600 "$HOME/.ssh/id_ed25519" "$HOME/.ssh/known_hosts"
      - name: Replace authorized server release
        run: |
          ssh -o BatchMode=yes -o StrictHostKeyChecking=yes "$DEPLOY_USER@$DEPLOY_HOST" bash -s -- "$IMAGE_REF" <<'REMOTE'
          set -euo pipefail
          cd "$HOME/lesson-release"
          export WEB_IMAGE="$1"
          docker compose config --quiet
          docker compose pull web
          docker compose up -d --wait --wait-timeout 120 web
          curl --fail --silent --show-error http://127.0.0.1:8080/ -o /dev/null
          printf 'WEB_IMAGE=%s\n' "$WEB_IMAGE" > .env
          REMOTE
      - name: Remove temporary SSH files
        if: always()
        run: rm -f "$HOME/.ssh/id_ed25519" "$HOME/.ssh/known_hosts"
```

## Step-by-step walkthrough

1. Prepare a trusted practice repository with the complete frontend Dockerfile from Multi-stage Docker Builds, a locked npm dependency graph, and a typecheck script. In that Dockerfile replace `RUN npm run build` with `RUN npm run typecheck && npm run build`. Review base-image digests and action updates before production adoption.

2. Provision the staging-like server from Deployment to Ubuntu Server, including its ~/lesson-release/compose.yaml and a registry pull identity for private images. Configure a verified deployment SSH key for that account. This workflow overwrites only the dedicated release directory’s .env with WEB_IMAGE; do not store application secrets in that file.

3. In GitHub configure production with required approval and allowed deployment branches where supported. Set DEPLOY_HOST and DEPLOY_USER variables; set DEPLOY_KEY and DEPLOY_KNOWN_HOSTS secrets. The known-hosts value must be obtained and verified through a trusted independent channel. Never disable SSH host-key checking or blindly trust an online key scan.

4. The build job validates types during image construction and starts the resulting image. The HTTP check verifies the root response with bounded retry; add frontend tests and content/API checks appropriate to the app.

5. Only after the smoke check does the token authenticate and push the same local image. A job output carries the real registry digest reference to the deployment job. No image rebuild occurs on the server.

6. After required environment review, SSH sends the image reference to a fixed remote Bash script. The script enters the dedicated release directory, pulls, replaces web, waits for health, checks HTTP, then records the active digest. If replacement fails, inspect and use the rollback procedure; this workflow does not automatically reverse an unhealthy release.

7. Concurrency limits overlapping releases in this workflow; pending runs may be superseded and ordering is not guaranteed. SSH file cleanup runs on the fresh hosted deployment VM after completion.

## Expected result

An approved successful run publishes a tested digest and runs that exact frontend artifact on the configured server. Runtime checks and operator review still determine real acceptance.

## Practice

Use a disposable target to simulate build failure, smoke-test failure, rejected approval, and missing server registry access. Success: identify the last completed boundary and confirm no earlier failed candidate is silently treated as deployed.

## Common errors and troubleshooting

Push denied: inspect package permissions. Digest empty: stop and inspect push metadata. SSH mismatch: investigate the server identity rather than suppressing checking. Server healthy but public site broken: inspect external TLS/proxy routes and browser checks. A failed deploy may leave the candidate running; use the documented compatibility-aware rollback.

## Knowledge check

1. What crosses jobs?
2. Does the server rebuild?
3. What gates deployment?
4. Does failed deployment auto-rollback?
5. What verifies SSH identity?

> [!question]- Answers
> 1. The real image digest output.
> 2. No.
> 3. Build success and configured environment protection.
> 4. No.
> 5. An independently verified known_hosts entry.

## Cleanup

Temporary runner container, registry login, and SSH files are cleaned up. The published package and running server intentionally persist. Their deletion or shutdown is a separate operator decision. Keep the previous digest and backups.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[02 - Docker Development/07 - Multi-stage Docker Builds|Multi-stage Docker Builds]]
- [[05 - Advanced CI CD/01 - Deployment to Ubuntu Server|Deployment to Ubuntu Server]]
- [[05 - Advanced CI CD/05 - Deployment Approvals|Deployment Approvals]]
- [[05 - Advanced CI CD/07 - Deployment Rollback|Deployment Rollback]]
- [[04 - Docker and GitHub Actions/07 - React TypeScript Build Validation|React TypeScript Build Validation]]
- [[06 - Practical Examples/01 - Dockerfile Examples|Next in the roadmap: Dockerfile Examples]]

## Official references

- [Publish official documentation](https://docs.github.com/en/actions/tutorials/publish-packages/publish-docker-images)
- [Env official documentation](https://docs.github.com/en/actions/how-tos/deploy/configure-and-manage-deployments/manage-environments)
- [Registry official documentation](https://docs.github.com/en/packages/working-with-a-github-packages-registry/working-with-the-container-registry)
- [Security official documentation](https://docs.github.com/en/actions/reference/security/secure-use)
- [Health official documentation](https://docs.docker.com/compose/how-tos/startup-order/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[05 - Advanced CI CD/09 - Docker Security Scanning\|Docker Security Scanning]] | [[06 - Practical Examples/01 - Dockerfile Examples\|Dockerfile Examples]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
