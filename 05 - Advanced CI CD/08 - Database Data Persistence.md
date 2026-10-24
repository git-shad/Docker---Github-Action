---
title: "Database Data Persistence"
subject: "GitHub Actions"
level: advanced
tags:
  - github-actions
  - advanced
---

# Database Data Persistence

Create a logical backup in a disposable lab and restore it into a separate database.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Create a logical backup in a disposable lab and restore it into a separate database. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I back up and restore MariaDB data beyond a container lifecycle?

## Prerequisites

Workflow examples target GitHub Actions on `ubuntu-24.04`; shell commands use Bash unless explicitly executed with `sh`. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

Earlier lessons: [[02 - Docker Development/03 - MariaDB Container|MariaDB Container]], [[01 - Docker Fundamentals/05 - Docker Volumes|Docker Volumes]].

## Introduction, purpose, and how it works

A volume survives container replacement but can still be deleted, corrupted, or lost with its host. A logical dump records SQL to reconstruct data. A backup is useful only if it can be restored, is sufficiently current, and is stored safely off the failure domain. --single-transaction is useful for transactional tables, but concurrent schema changes and nontransactional tables require extra coordination. Recovery point objective means acceptable data loss; recovery time objective means acceptable recovery duration.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `mariadb-dump --single-transaction` | Create a transactional logical snapshot | Use with its consistency limits understood. |
| `chmod 600 FILE` | Restrict file access to its owner | Use for sensitive backups; also protect directories and off-host storage. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```bash
mkdir -p backups
chmod 700 backups
docker compose exec db sh -c 'mariadb-dump -u lesson -p --single-transaction lesson > /tmp/lesson.sql'
docker compose cp db:/tmp/lesson.sql ./backups/lesson.sql
chmod 600 backups/lesson.sql
docker compose exec db mariadb -u root -p -e 'CREATE DATABASE lesson_restore;'
docker compose cp ./backups/lesson.sql db:/tmp/lesson-restore.sql
docker compose exec db sh -c 'mariadb -u root -p lesson_restore < /tmp/lesson-restore.sql'
docker compose exec db mariadb -u root -p lesson_restore -e 'SHOW TABLES;'
```

## Step-by-step walkthrough

1. Use only the local MariaDB Container lab after creating its notes table. Passwords are prompted interactively; these commands are not an unattended CI backup job.

2. The dump is written inside the container so the interactive password prompt remains available. Copy it to a protected host directory; the copy may include sensitive rows in real systems.

3. Create lesson_restore as a separate target. Do not restore over an active database for a learning exercise.

4. Copy the dump back under a separate temporary filename and import into lesson_restore. Verify tables, row counts, and representative application reads; a successful import alone does not prove full recovery.

5. For real systems add encryption, off-host storage, retention, restricted accounts, scheduled backups, restore drills, and an agreed recovery objective. Avoid raw filesystem copies of a running database volume unless using a database-supported consistent backup method.

## Expected result

The separate restore database contains the lab schema. The original lesson database is unchanged.

## Practice

Insert a known row before backup and verify it after restoration. Success: demonstrate a real row round trip and measure elapsed restore time.

## Common errors and troubleshooting

Insufficient dump privileges: inspect database version and dump requirements, then grant narrowly scoped backup privileges. Failed prompt in automation: use protected option/secret files and a dedicated backup account rather than embedding a password in CLI text.

## Knowledge check

1. Is a volume a backup?
2. What does RPO mean?
3. What does RTO mean?
4. Why restore separately?
5. What proves recovery?

> [!question]- Answers
> 1. No.
> 2. Acceptable data loss.
> 3. Acceptable recovery time.
> 4. Avoid overwriting active data.
> 5. Verified restored schema and data with application checks.

## Cleanup

After confirming restoration, remove only the lab temporary dump files and disposable backup when no longer needed. DROP DATABASE lesson_restore permanently deletes the restored lab database; use it only on the disposable instance.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[GitHub Actions/GitHub Actions Index|GitHub Actions learning path]]
- [[GitHub Actions/GitHub Actions Questions|GitHub Actions questions]]
- [[02 - Docker Development/03 - MariaDB Container|MariaDB Container]]
- [[01 - Docker Fundamentals/05 - Docker Volumes|Docker Volumes]]
- [[05 - Advanced CI CD/09 - Docker Security Scanning|Next in the roadmap: Docker Security Scanning]]

## Official references

- [Volume official documentation](https://docs.docker.com/engine/storage/volumes/)
- [Maria official documentation](https://mariadb.com/kb/en/using-healthcheck-sh/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[05 - Advanced CI CD/07 - Deployment Rollback\|Deployment Rollback]] | [[05 - Advanced CI CD/09 - Docker Security Scanning\|Docker Security Scanning]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
