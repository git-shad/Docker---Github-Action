# Docker & GitHub Actions learning vault

## Purpose

Build and maintain this folder as an Obsidian knowledge vault for learning Docker and GitHub Actions, from introduction to advanced practice. Make it easy to find a question, open its tutorial, and follow a clear learning path.

Use `AGENTS.md` as the instruction file for agents working in this vault. Keep learning content in ordinary Markdown files that work in Obsidian.

## Folder structure

Keep the two subjects separate. Use this structure when creating learning content:

```text
Home.md
Docker/
  Docker Index.md
  Docker Questions.md
  01 - Introduction/
  02 - Fundamentals/
  03 - Intermediate/
  04 - Advanced/
  05 - Projects/
GitHub Actions/
  GitHub Actions Index.md
  GitHub Actions Questions.md
  01 - Introduction/
  02 - Fundamentals/
  03 - Intermediate/
  04 - Advanced/
  05 - Projects/
Attachments/
```

- `Home.md` is the vault entry point. Link to both subject indexes and both question lists.
- Each subject index presents an ordered learning path, grouped by level, with links to tutorials.
- Each subject question list groups searchable questions by level and links every answered question to its tutorial.
- Each tutorial lives in its own Markdown file under the appropriate subject and level.
- Store images and other supporting assets in `Attachments/`, with descriptive names.
- Create folders and files as content is added. Do not generate empty tutorials or broken links to make the vault appear complete.
- Keep Docker-and-GitHub-Actions integration tutorials under `GitHub Actions/05 - Projects/` and link to the required Docker lessons.

## Learning coverage

Build lessons in prerequisite order. Treat these topics as a roadmap, not a requirement to generate the entire vault in one task.

### Docker

1. Introduction: what Docker solves, containers versus virtual machines, installation, and running a first container.
2. Fundamentals: images, containers, lifecycle commands, ports, environment variables, logs, and Dockerfiles.
3. Intermediate: volumes, bind mounts, networks, Docker Compose, registries, and multi-stage builds.
4. Advanced: build caching, image optimization, non-root users, secrets, resource limits, health checks, debugging, and production considerations.
5. Projects: containerize an application, run a multi-service stack, and build and publish an image.

### GitHub Actions

1. Introduction: CI/CD concepts, what GitHub Actions does, and a first workflow.
2. Fundamentals: workflow YAML, events, jobs, steps, runners, actions, and expressions.
3. Intermediate: dependencies between jobs, matrices, artifacts, caches, variables, secrets, and environments.
4. Advanced: reusable workflows, composite actions, permissions, OIDC, concurrency, deployment approvals, workflow security, and troubleshooting.
5. Projects: automated tests, Docker image builds and publishing, and deployment workflows.

## Question lists

Use real learner questions as the navigation labels. Examples include “What is the difference between an image and a container?” and “How do I run a workflow only for pull requests?”

Use this format within each level:

```markdown
## Fundamentals

- [[Docker/02 - Fundamentals/Images and Containers|What is the difference between an image and a container?]]
```

For roadmap questions whose tutorials do not exist yet, use plain text with `(planned)` instead of a link. Replace the plain text with a link when the tutorial is written. Several closely related questions may link to one tutorial; split unrelated topics into separate files.

## Tutorial format

Use clear English, short explanations, and practical examples. Assume no prior knowledge in introductory lessons. Explain new terminology before using it.

Start each tutorial with Obsidian-compatible properties:

```yaml
---
title: Images and Containers
subject: Docker
level: fundamentals
tags:
  - docker
  - fundamentals
---
```

Then include:

1. A single H1 title and a short statement of what the learner will accomplish.
2. **Questions answered**: the specific questions this tutorial addresses.
3. **Prerequisites**: required tools, accounts, and links to earlier lessons.
4. **Explanation**: concepts with concrete examples.
5. **Walkthrough**: numbered steps, complete code or commands, filenames, and where to run them.
6. **Expected result**: what successful execution looks like; label illustrative output as an example.
7. **Practice**: a small exercise with an observable success condition.
8. **Troubleshooting**: common errors, their causes, and fixes.
9. **Cleanup**: how to remove resources created by the tutorial, where applicable.
10. **Related lessons**: links to the subject index, question list, prerequisites, and existing next lessons.
11. **References**: direct links to official documentation used for the lesson.

Omit sections only when they do not apply. Use fenced code blocks with language labels such as `bash`, `yaml`, and `dockerfile`. Never present pseudocode as a runnable example.

## Obsidian navigation

- Use vault-relative wikilinks, without the `.md` extension, for internal navigation: `[[Docker/Docker Index|Docker learning path]]`.
- Use descriptive filenames and link labels. Use full vault-relative paths to avoid ambiguity between subjects.
- Use standard Markdown links for external references.
- Keep important explanations in the tutorial itself so it remains understandable without plugins.
- Avoid mandatory community plugins, custom HTML, and unnecessary folder nesting.
- When adding or renaming a tutorial, update its subject index, question list, and affected links in the same change.

## Accuracy and safe examples

- Check current official Docker and GitHub documentation when writing version-sensitive instructions. Include a `verified_on: YYYY-MM-DD` property only when the references were actually checked.
- State the operating system, shell, and tool versions when they affect the steps. Distinguish Linux, macOS, Windows PowerShell, and WSL instructions where needed.
- Use placeholders for credentials, repository owners, registry names, and deployment targets. Never include real secrets.
- Explain commands that delete data, expose services, create paid resources, or publish artifacts before showing them.
- Prefer minimal GitHub token permissions and explain any additional permissions. Use secure action-pinning practices supported by current GitHub guidance.
- Label local demonstrations separately from production guidance. Explain the additional requirements before describing a setup as production-ready.
- Do not install tools, run containers, publish images, trigger workflows, or deploy resources merely to write a tutorial unless the user authorized those actions.
- Distinguish examples checked by reading documentation from examples actually executed. Never claim a command or workflow passed without evidence.

## Workflow for future changes

1. Read this file and inspect existing notes before making changes.
2. Follow the user's requested scope; preserve existing content and organization.
3. For an initial vault build, create `Home.md`, both indexes, both question lists, and introductory tutorials for both subjects. Mark remaining roadmap questions as planned.
4. For later work, add or improve complete tutorials in prerequisite order or as requested.
5. Review Markdown formatting, code-block language labels, navigation, duplicate topics, and internal link targets.
6. If Git is available, review the diff and status and run `git diff --check`. Do not commit or push without explicit instruction.
7. Report what was created or updated and what validation was performed. Clearly state any remaining planned content or untested examples.
