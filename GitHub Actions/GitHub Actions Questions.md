# GitHub Actions questions

[[Home|Home]] · [[GitHub Actions/GitHub Actions Index|Ordered learning path]]

Search this note for your question, then open its linked tutorial.

## 03 - GitHub Actions Fundamentals

- [[03 - GitHub Actions Fundamentals/01 - Introduction to CI CD|What is the difference between CI, continuous delivery, and continuous deployment?]]
- [[03 - GitHub Actions Fundamentals/02 - GitHub Actions Workflow|What belongs in a GitHub Actions workflow YAML file?]]
- [[03 - GitHub Actions Fundamentals/03 - Jobs Steps and Actions|What is the difference between a job, a step, and an action?]]
- [[03 - GitHub Actions Fundamentals/04 - GitHub Runners|Should I use a GitHub-hosted runner or a self-hosted runner?]]
- [[03 - GitHub Actions Fundamentals/05 - Workflow Events|How do I run a workflow only for pull requests to main?]]
- [[03 - GitHub Actions Fundamentals/06 - Uses vs Run|When should I use uses and when should I use run?]]
- [[03 - GitHub Actions Fundamentals/07 - Secrets and Variables|What is the difference between GitHub Actions secrets and variables?]]
- [[03 - GitHub Actions Fundamentals/08 - Troubleshooting Workflows|How do I find the real cause of a failed workflow?]]

## 04 - Docker and GitHub Actions

- [[04 - Docker and GitHub Actions/01 - Docker in GitHub Actions|How does a workflow use Docker on its runner?]]
- [[04 - Docker and GitHub Actions/02 - Automatic Docker Builds|How do I build an image automatically without publishing it?]]
- [[04 - Docker and GitHub Actions/03 - Running Containers in CI|How do I verify a built image actually runs?]]
- [[04 - Docker and GitHub Actions/04 - Docker Compose in CI|How do I run a Laravel and MariaDB stack in CI?]]
- [[04 - Docker and GitHub Actions/05 - Health Checks in CI|How do I stop tests from racing database startup?]]
- [[04 - Docker and GitHub Actions/06 - Laravel Automated Testing|How do I keep Laravel tests isolated from real data?]]
- [[04 - Docker and GitHub Actions/07 - React TypeScript Build Validation|How do I validate a React TypeScript frontend in CI?]]
- [[04 - Docker and GitHub Actions/08 - Container Cleanup|How do I clean up CI containers without deleting unrelated data?]]
- [[04 - Docker and GitHub Actions/09 - Publishing Docker Images|How do I publish a tested image to GitHub Container Registry?]]
- [[04 - Docker and GitHub Actions/10 - Preventing Failed Deployments|How do I stop a failed validation job from reaching deployment?]]

## 05 - Advanced CI CD

- [[05 - Advanced CI CD/01 - Deployment to Ubuntu Server|How do I deploy a tested static web image to an Ubuntu server?]]
- [[05 - Advanced CI CD/02 - GitHub Container Registry|How are GHCR packages, repositories, tags, and digests related?]]
- [[05 - Advanced CI CD/03 - Docker Image Versioning|Should I deploy latest, a commit tag, a release tag, or a digest?]]
- [[05 - Advanced CI CD/04 - Staging and Production|How do I promote the same image through staging and production?]]
- [[05 - Advanced CI CD/05 - Deployment Approvals|How do required reviewers protect a production deployment?]]
- [[05 - Advanced CI CD/06 - Container Registry Authentication|Which credentials should CI and servers use to access GHCR?]]
- [[05 - Advanced CI CD/07 - Deployment Rollback|How do I roll back a bad container release safely?]]
- [[05 - Advanced CI CD/08 - Database Data Persistence|How do I back up and restore MariaDB data beyond a container lifecycle?]]
- [[05 - Advanced CI CD/09 - Docker Security Scanning|How do I scan an image and use vulnerability results as a release gate?]]
- [[05 - Advanced CI CD/10 - Complete CI CD Pipeline|How do I connect type validation, image publishing, approval, and server deployment?]]

## 06 - Practical Examples

- [[06 - Practical Examples/01 - Dockerfile Examples|How do I choose a Dockerfile for a PHP command or a static frontend?]]
- [[06 - Practical Examples/02 - Docker Compose Examples|How do I choose between a frontend-only and a Laravel database Compose stack?]]
- [[06 - Practical Examples/03 - GitHub Actions YAML Examples|How can I fan out validation and gate a later job?]]
- [[06 - Practical Examples/04 - Laravel React MariaDB Architecture|How do React, Laravel, and MariaDB communicate in a containerized application?]]
- [[06 - Practical Examples/05 - CI CD Pipeline Walkthrough|How do I review a release pipeline before trusting it?]]
- [[06 - Practical Examples/06 - Common Errors and Solutions|How do I distinguish build, network, database, and workflow errors?]]

## 07 - References

- [[07 - References/04 - GitHub Actions YAML Reference|What are the core GitHub Actions workflow keys?]]
- [[07 - References/05 - DevOps Glossary|What do the common Docker and CI/CD terms mean?]]

## Advanced extensions

- How do I create a reusable workflow and a composite action? (planned)
- How do I configure restrictive OIDC trust for a specific cloud provider? (planned)
- How do I verify artifact attestations and protect caches from poisoning? (planned)

## Examples

- [[GitHub Actions/Examples/GitHub Actions Examples|Browse GitHub Actions examples]]
