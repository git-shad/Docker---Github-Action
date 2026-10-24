---
title: GitHub Actions Examples
subject: GitHub Actions
tags:
  - github-actions
  - examples
---

# GitHub Actions examples

Choose a workflow example below. Each linked lesson explains its complete YAML, repository prerequisites, expected behavior, failure cases, and cleanup. Start with the shell-only workflows before moving to application checks and releases.

## First workflows

- [[03 - GitHub Actions Fundamentals/01 - Introduction to CI CD|Run a manual greeting workflow]]
- [[03 - GitHub Actions Fundamentals/03 - Jobs Steps and Actions|Make one job depend on another]]
- [[03 - GitHub Actions Fundamentals/05 - Workflow Events|Run a check only for pull requests to main]]
- [[03 - GitHub Actions Fundamentals/06 - Uses vs Run|Combine a pinned checkout action with a shell command]]

## Application and container checks

- [[04 - Docker and GitHub Actions/02 - Automatic Docker Builds|Build a Docker image without publishing it]]
- [[04 - Docker and GitHub Actions/04 - Docker Compose in CI|Run Laravel tests with a disposable MariaDB dependency]]
- [[04 - Docker and GitHub Actions/07 - React TypeScript Build Validation|Validate frontend types and compile a React build]]
- [[06 - Practical Examples/03 - GitHub Actions YAML Examples|Check multiple Node versions with a matrix]]
- [[04 - Docker and GitHub Actions/08 - Container Cleanup|Clean up a job-owned Compose project after failure]]

## Publishing and deployment

These examples can change an external registry or server when copied into a configured repository and triggered. Read their permissions, target configuration, approval, and recovery requirements first.

- [[04 - Docker and GitHub Actions/09 - Publishing Docker Images|Publish a tested image to GHCR]]
- [[04 - Docker and GitHub Actions/10 - Preventing Failed Deployments|Explore validation and environment gates without deploying]]
- [[05 - Advanced CI CD/10 - Complete CI CD Pipeline|Connect frontend validation, publishing, approval, and server deployment]]
- [[06 - Practical Examples/05 - CI CD Pipeline Walkthrough|Review evidence and failure boundaries in the release pipeline]]

## Before trying an example

The workflows have not been executed for this vault. Use a practice repository and the toolchain described in the linked lesson; hosted runs may consume your account allowance. Docker integration examples require the Docker lessons listed in their prerequisites. Workflow configuration remains inside Markdown code blocks here.

## Navigation

- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions learner questions]]
- [[Docker/Examples/Docker Examples|Docker examples]]
- [[00 - Learning Roadmap|Complete learning checklist]]

## Docker configuration prerequisites

- [[08 - Dockerfile and Compose Mastery/00 - Mastery Roadmap|Master Dockerfile and Compose before using them in CI]]
