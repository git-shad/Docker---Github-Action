---
title: "Docker Security Scanning"
subject: "GitHub Actions"
level: advanced
tags:
  - github-actions
  - advanced
---

# Docker Security Scanning

Inspect dependencies and fail a scan for an explicitly chosen severity policy.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Inspect dependencies and fail a scan for an explicitly chosen severity policy. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I scan an image and use vulnerability results as a release gate?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[05 - Advanced CI CD/03 - Docker Image Versioning|Docker Image Versioning]].

## Introduction, purpose, and how it works

A vulnerability scanner inventories packages and compares them with advisory data. A CVE is a published vulnerability identifier; severity estimates potential impact, not certainty that your application is exploitable. Results change as advisory databases improve even when the image stays unchanged. Scan the actual release digest, triage findings, rebuild patched dependencies, and track time-limited documented exceptions rather than silently suppressing failures.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `--only-severity critical,high` | Filter selected severities | Choose a documented policy, not an arbitrary silence threshold. |
| `--exit-code` | Fail when selected vulnerabilities exist | Use for enforcement and preserve the process result. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```bash
docker scout version
read -r -p "Image reference to scan: " IMAGE_REF
docker scout cves --only-severity critical,high --exit-code "$IMAGE_REF"
```

## Step-by-step walkthrough

1. Use Ubuntu Bash with Docker Scout already available and authorized for the chosen local image or registry package. Installation and service enrollment are separate decisions; review account and service usage before enabling scanning.

2. Select a real image or digest, preferably the candidate you intend to release. Scanners may access remote advisory services; review data and service policy.

3. The severity filter focuses the report, and --exit-code makes matching findings return nonzero. Without an enforced exit status a report alone may not block publishing.

4. Review package, fixed version, affected code path, and exception expiry. Rebuild and rescan the corrected artifact, then rerun tests. A scan is one layer alongside non-root execution, secrets handling, patching, and isolation.

## Expected result

The scan reports findings and exits according to the selected policy. No clean result is asserted for any image in this vault.

## Practice

Compare a current candidate with a deliberately older disposable image. Success: identify one finding and its remediation or documented applicability assessment.

## Common errors and troubleshooting

Command unavailable: verify approved tooling rather than installing silently. Auth failure: check registry/Scout access separately. No findings does not prove absence of vulnerabilities or safe runtime configuration.

## Knowledge check

1. What is a CVE?
2. Does severity prove exploitation?
3. Why scan a digest?
4. Can results change without a rebuild?
5. What makes a report a gate?

> [!question]- Answers
> 1. A vulnerability identifier.
> 2. No.
> 3. To assess the exact artifact.
> 4. Yes, advisory data evolves.
> 5. A nonzero policy-failure exit status enforced by CI.

## Cleanup

No server is deployed. Log out of temporary registry sessions and retain reports under an appropriate access/retention policy.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[05 - Advanced CI CD/03 - Docker Image Versioning|Docker Image Versioning]]
- [[05 - Advanced CI CD/10 - Complete CI CD Pipeline|Next in the roadmap: Complete CI CD Pipeline]]

## Official references

- [Scout official documentation](https://docs.docker.com/scout/explore/analysis/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[05 - Advanced CI CD/08 - Database Data Persistence\|Database Data Persistence]] | [[05 - Advanced CI CD/10 - Complete CI CD Pipeline\|Complete CI CD Pipeline]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
