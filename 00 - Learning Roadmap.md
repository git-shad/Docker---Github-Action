# Learning roadmap

[[Home|Vault home]] · [[Docker/Docker Index|Docker path]] · [[GitHub Actions/GitHub Actions Index|Actions path]]

Study the sections in order. Each tutorial is a complete note; checkboxes record your own progress. Where an exercise needs an existing application, use a disposable checkout and read its prerequisites first. References are learning aids and can be revisited at any point.

## 01 - Docker Fundamentals

- [ ] [[01 - Docker Fundamentals/01 - Introduction to Docker|Introduction to Docker]]
- [ ] [[01 - Docker Fundamentals/02 - Images and Containers|Images and Containers]]
- [ ] [[01 - Docker Fundamentals/03 - Dockerfile Instructions|Dockerfile Instructions]]
- [ ] [[01 - Docker Fundamentals/04 - Docker Compose|Docker Compose]]
- [ ] [[01 - Docker Fundamentals/05 - Docker Volumes|Docker Volumes]]
- [ ] [[01 - Docker Fundamentals/06 - Docker Networks|Docker Networks]]
- [ ] [[01 - Docker Fundamentals/07 - Docker Commands|Docker Commands]]
- [ ] [[Docker/02 - Fundamentals/Docker CLI Flags and Combinations|Docker CLI Flags and Combinations]]

## 02 - Docker Development

- [ ] [[02 - Docker Development/01 - Laravel Docker Environment|Laravel Docker Environment]]
- [ ] [[02 - Docker Development/02 - PHP Composer and Node.js|PHP Composer and Node.js]]
- [ ] [[02 - Docker Development/03 - MariaDB Container|MariaDB Container]]
- [ ] [[02 - Docker Development/04 - Laravel and MariaDB Networking|Laravel and MariaDB Networking]]
- [ ] [[02 - Docker Development/05 - Environment Variables|Environment Variables]]
- [ ] [[02 - Docker Development/06 - Bind Mounts and Hot Reload|Bind Mounts and Hot Reload]]
- [ ] [[02 - Docker Development/07 - Multi-stage Docker Builds|Multi-stage Docker Builds]]
- [ ] [[02 - Docker Development/08 - Docker Health Checks|Docker Health Checks]]
- [ ] [[02 - Docker Development/09 - Development vs Production|Development vs Production]]

## 03 - GitHub Actions Fundamentals

- [ ] [[03 - GitHub Actions Fundamentals/01 - Introduction to CI CD|Introduction to CI CD]]
- [ ] [[03 - GitHub Actions Fundamentals/02 - GitHub Actions Workflow|GitHub Actions Workflow]]
- [ ] [[03 - GitHub Actions Fundamentals/03 - Jobs Steps and Actions|Jobs Steps and Actions]]
- [ ] [[03 - GitHub Actions Fundamentals/04 - GitHub Runners|GitHub Runners]]
- [ ] [[03 - GitHub Actions Fundamentals/05 - Workflow Events|Workflow Events]]
- [ ] [[03 - GitHub Actions Fundamentals/06 - Uses vs Run|Uses vs Run]]
- [ ] [[03 - GitHub Actions Fundamentals/07 - Secrets and Variables|Secrets and Variables]]
- [ ] [[03 - GitHub Actions Fundamentals/08 - Troubleshooting Workflows|Troubleshooting Workflows]]

## 04 - Docker and GitHub Actions

- [ ] [[04 - Docker and GitHub Actions/01 - Docker in GitHub Actions|Docker in GitHub Actions]]
- [ ] [[04 - Docker and GitHub Actions/02 - Automatic Docker Builds|Automatic Docker Builds]]
- [ ] [[04 - Docker and GitHub Actions/03 - Running Containers in CI|Running Containers in CI]]
- [ ] [[04 - Docker and GitHub Actions/04 - Docker Compose in CI|Docker Compose in CI]]
- [ ] [[04 - Docker and GitHub Actions/05 - Health Checks in CI|Health Checks in CI]]
- [ ] [[04 - Docker and GitHub Actions/06 - Laravel Automated Testing|Laravel Automated Testing]]
- [ ] [[04 - Docker and GitHub Actions/07 - React TypeScript Build Validation|React TypeScript Build Validation]]
- [ ] [[04 - Docker and GitHub Actions/08 - Container Cleanup|Container Cleanup]]
- [ ] [[04 - Docker and GitHub Actions/09 - Publishing Docker Images|Publishing Docker Images]]
- [ ] [[04 - Docker and GitHub Actions/10 - Preventing Failed Deployments|Preventing Failed Deployments]]

## 05 - Advanced CI CD

- [ ] [[05 - Advanced CI CD/01 - Deployment to Ubuntu Server|Deployment to Ubuntu Server]]
- [ ] [[05 - Advanced CI CD/02 - GitHub Container Registry|GitHub Container Registry]]
- [ ] [[05 - Advanced CI CD/03 - Docker Image Versioning|Docker Image Versioning]]
- [ ] [[05 - Advanced CI CD/04 - Staging and Production|Staging and Production]]
- [ ] [[05 - Advanced CI CD/05 - Deployment Approvals|Deployment Approvals]]
- [ ] [[05 - Advanced CI CD/06 - Container Registry Authentication|Container Registry Authentication]]
- [ ] [[05 - Advanced CI CD/07 - Deployment Rollback|Deployment Rollback]]
- [ ] [[05 - Advanced CI CD/08 - Database Data Persistence|Database Data Persistence]]
- [ ] [[05 - Advanced CI CD/09 - Docker Security Scanning|Docker Security Scanning]]
- [ ] [[05 - Advanced CI CD/10 - Complete CI CD Pipeline|Complete CI CD Pipeline]]

## 06 - Practical Examples

- [ ] [[06 - Practical Examples/01 - Dockerfile Examples|Dockerfile Examples]]
- [ ] [[06 - Practical Examples/02 - Docker Compose Examples|Docker Compose Examples]]
- [ ] [[06 - Practical Examples/03 - GitHub Actions YAML Examples|GitHub Actions YAML Examples]]
- [ ] [[06 - Practical Examples/04 - Laravel React MariaDB Architecture|Laravel React MariaDB Architecture]]
- [ ] [[06 - Practical Examples/05 - CI CD Pipeline Walkthrough|CI CD Pipeline Walkthrough]]
- [ ] [[06 - Practical Examples/06 - Common Errors and Solutions|Common Errors and Solutions]]

## 07 - References

- [ ] [[07 - References/01 - Docker Command Cheat Sheet|Docker Command Cheat Sheet]]
- [ ] [[07 - References/02 - Dockerfile Instruction Reference|Dockerfile Instruction Reference]]
- [ ] [[07 - References/03 - Docker Compose YAML Reference|Docker Compose YAML Reference]]
- [ ] [[07 - References/04 - GitHub Actions YAML Reference|GitHub Actions YAML Reference]]
- [ ] [[07 - References/05 - DevOps Glossary|DevOps Glossary]]

## Verification boundary

Official Docker and GitHub references and selected Laravel/MariaDB pages were consulted on 2026-10-21. This date records documentation review, not execution, exhaustive external-link availability, dependency compatibility, or deployment certification. No tutorial examples were run. Action checkout is deliberately pinned to its official v4.2.2 commit; it is not presented as the newest release. Review newer revisions and their requirements before adoption.

## Optional future coverage

The requested 55 topics are written. Dedicated deep dives into reusable workflows, composite actions, OIDC provider trust, cache poisoning, artifact attestations, advanced BuildKit caching, and zero-downtime database migrations remain planned; these are not broken tutorial links.

## Browse examples by subject

- [[Docker/Examples/Docker Examples|Docker examples]]
- [[GitHub Actions/Examples/GitHub Actions Examples|GitHub Actions examples]]

## 08 - Dockerfile and Compose Mastery

- [ ] [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Mastery roadmap and complete module checklist]]

This expansion adds 30 lessons and its own roadmap to the original 55-lesson path. It covers complete instruction/property references, corrected anti-patterns, a full-stack local example, 15 progressive exercises, and a final assessment. Examples remain unexecuted documentation.

## Advanced Docker Engine configuration

- [ ] [[Docker/04 - Advanced/Docker Daemon Flags and Configuration|Docker Daemon Flags and Configuration]]

The flags extension adds two tutorials to the existing path. Read the CLI lesson after Docker Commands; the advanced daemon lesson follows the mastery module in Previous/Next navigation and links its storage, networking, and security prerequisites. Its temporary-file validation and optional VM service-change exercises are separate. Official references for these two lessons were checked on 2026-10-24; their commands were not executed.
