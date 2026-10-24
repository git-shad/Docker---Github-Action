---
title: "MariaDB Container"
subject: "Docker"
level: intermediate
tags:
  - docker
  - intermediate
---

# MariaDB Container

Initialize a local database with a non-root application account.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Initialize a local database with a non-root application account. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I start MariaDB with a persistent data directory?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). PHP examples use PHP 8.4; frontend examples use Node 22; database examples use MariaDB 11.4. Check the project’s own support constraints. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[02 - Docker Development/02 - PHP Composer and Node.js|PHP Composer and Node.js]].

## Introduction, purpose, and how it works

MariaDB is a database server; its image contains software, while a mounted data directory contains the databases. On first initialization the image entrypoint creates requested users and databases. Initialization environment variables normally do not reconfigure an already-initialized volume. The application should use a limited account, not the database administrator.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `MARIADB_DATABASE` | Name a database for first initialization | Use on an empty data directory; it does not migrate existing schema. |
| `MARIADB_USER` | Create an application account | Use with a password; do not set it to root. |
| `healthcheck.sh` | Probe database readiness | Use the image-provided helper with supported options. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```yaml
services:
  db:
    image: mariadb:11.4
    environment:
      MARIADB_DATABASE: lesson
      MARIADB_USER: lesson
      MARIADB_PASSWORD: local-only-password
      MARIADB_ROOT_PASSWORD: local-only-root-password
    volumes:
      - lesson-db:/var/lib/mysql
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      interval: 5s
      timeout: 3s
      retries: 20
volumes:
  lesson-db:
```

## Step-by-step walkthrough

1. Use a new local directory and save this as compose.yaml. These public passwords are for a disposable lab only; no database port is published.

2. Run `docker compose up -d --wait --wait-timeout 180`. The volume persists outside the container layer.

3. Connect using `docker compose exec db mariadb -u lesson -p lesson`, entering the lab password at the prompt. Run `SELECT DATABASE();` then `exit`.

4. Stop and start with `docker compose down` and `docker compose up -d --wait`; the same named volume is reused under the same Compose project.

## Expected result

The query returns lesson and the database survives container recreation.

## Practice

Create a table with `CREATE TABLE notes (id INT PRIMARY KEY);`, recreate the container, then run `SHOW TABLES;`. Success: notes remains.

## Common errors and troubleshooting

Access denied after changing the YAML password: the old initialized volume still holds the old account password. Change credentials through SQL or reset only disposable data. Never delete a real volume as a password-reset shortcut.

## Knowledge check

1. What survives recreation?
2. Which port do peers use?
3. Do changed initialization values reset an existing user?
4. Why avoid root for the app?
5. Is a published port required?

> [!question]- Answers
> 1. The named volume.
> 2. 3306.
> 3. No.
> 4. Limit database privileges.
> 5. No.

## Cleanup

`docker compose down` retains data. `docker compose down --volumes` permanently erases this lab’s named volume; use it only after confirming no valuable data remains.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[02 - Docker Development/02 - PHP Composer and Node.js|PHP Composer and Node.js]]
- [[02 - Docker Development/04 - Laravel and MariaDB Networking|Next in the roadmap: Laravel and MariaDB Networking]]

## Official references

- [Maria official documentation](https://mariadb.com/kb/en/using-healthcheck-sh/)
- [Volume official documentation](https://docs.docker.com/engine/storage/volumes/)
- [Compose official documentation](https://docs.docker.com/reference/compose-file/services/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[02 - Docker Development/02 - PHP Composer and Node.js\|PHP Composer and Node.js]] | [[02 - Docker Development/04 - Laravel and MariaDB Networking\|Laravel and MariaDB Networking]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
