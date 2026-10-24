---
title: Docker Examples
subject: Docker
tags:
  - docker
  - examples
---

# Docker examples

Choose a local example below. Each linked lesson contains the complete Markdown code examples, prerequisites, walkthrough, expected result, troubleshooting, and cleanup instructions. Follow them in this order if you are starting from scratch.

## First containers and images

- [[01 - Docker Fundamentals/01 - Introduction to Docker|Run your first disposable container]]
- [[Docker/02 - Fundamentals/Docker CLI Flags and Combinations|Compare -it, -dit, piped input, background services, and useful flag combinations]]
- [[01 - Docker Fundamentals/03 - Dockerfile Instructions|Build a small image and override its default arguments]]
- [[06 - Practical Examples/01 - Dockerfile Examples|Run a PHP command as a non-root user]]

## Services, storage, and networking

- [[01 - Docker Fundamentals/04 - Docker Compose|Run a local web service with Compose]]
- [[01 - Docker Fundamentals/05 - Docker Volumes|Preserve a file across disposable containers]]
- [[01 - Docker Fundamentals/06 - Docker Networks|Connect two containers through network DNS]]
- [[06 - Practical Examples/02 - Docker Compose Examples|Run a static frontend with a read-only filesystem]]

## Application development

- [[02 - Docker Development/01 - Laravel Docker Environment|Run an existing Laravel project with MariaDB]]
- [[02 - Docker Development/06 - Bind Mounts and Hot Reload|Read host edits through a bind mount]]
- [[02 - Docker Development/07 - Multi-stage Docker Builds|Build a React frontend and serve its compiled files]]
- [[06 - Practical Examples/04 - Laravel React MariaDB Architecture|Trace communication between React, Laravel, and MariaDB]]

## Engine configuration

- [[Docker/04 - Advanced/Docker Daemon Flags and Configuration|Validate daemon flags and JSON, then optionally apply and roll back logging defaults in a disposable VM]]

Follow the CLI flag lesson before the daemon extension. Temporary-file validation does not start a daemon; the optional service exercise restarts Docker and creates a test container only in the specified practice VM.

## Before trying an example

These are documentation examples, not executed results. Use the execution context stated in each lesson and a disposable project where requested. Read cleanup instructions before starting resources, especially when a command deletes volumes or database data. Configuration remains inside Markdown code blocks in this vault.

## Navigation

- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[GitHub Actions/Examples/GitHub Actions Examples|GitHub Actions examples]]
- [[00 - Learning Roadmap|Complete learning checklist]]

## Mastery exercises and stack example

- [[08 - Dockerfile and Compose Mastery/23 - Dockerfile and Compose Together|Write an image and service together]]
- [[08 - Dockerfile and Compose Mastery/24 - Laravel React MariaDB Example|Study the full-stack local example]]
- [[08 - Dockerfile and Compose Mastery/28 - Practice Exercises|Attempt 15 exercises with collapsible solutions]]
