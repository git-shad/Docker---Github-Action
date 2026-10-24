---
title: "Laravel Docker Environment"
subject: "Docker"
level: intermediate
tags:
  - docker
  - intermediate
---

# Laravel Docker Environment

Build a development PHP runtime and serve an existing Laravel checkout.

> [!info] Example status
> Written with official references; no Docker commands, installations, workflows, registry changes, or deployments were executed for this vault. Expected results are predictions, not captured test evidence. Version tags are readable learning defaults and can move; use reviewed digests for release inputs.

## Learning objectives

Build a development PHP runtime and serve an existing Laravel checkout. You will also identify the relevant configuration, recognize failure conditions, and complete a small practice task.

## Questions answered

- How do I run an existing Laravel application in Docker?

## Prerequisites

Examples target Ubuntu Linux with Bash, Docker Engine, and the Docker Compose v2 plugin (`docker compose`). PHP examples use PHP 8.4; frontend examples use Node 22; database examples use MariaDB 11.4. Check the project’s own support constraints. An internet connection and permission to use the selected practice environment are needed for downloads or remote operations. Windows users should use an appropriately configured WSL2 Ubuntu shell for these Bash examples; they are not PowerShell commands. macOS users need Docker Desktop or another Linux Engine and must adapt Linux-only UID/path behavior.

No earlier lesson is required. Read [[Docker/Docker Index|the Docker path]] for orientation.

## Introduction, purpose, and how it works

Laravel is a PHP framework. Composer installs PHP packages into `vendor/`; Artisan is Laravel’s project command-line entry point. A runtime image supplies PHP and extensions while a bind mount supplies your editable code. This separates host tool installation from application dependencies. This lab assumes an existing Laravel 13 project whose lock file permits PHP 8.4 and whose extension requirements have been reviewed. It does not create a framework application.

## Syntax and configuration

| Keyword or syntax | Definition and purpose | When to use it and common mistakes |
| --- | --- | --- |
| `FROM php:8.4-cli` | Select a PHP runtime | Use for this development lab; check project PHP constraints. |
| `docker-php-ext-install pdo_mysql` | Enable the MySQL database driver | Use when Laravel talks to MariaDB; changing the Dockerfile requires a rebuild. |
| `working_dir: /app` | Select command directory | Use so Composer and Artisan see their project files. |

The practical example below shows these concepts in context; do not run an isolated reference row without its stated prerequisites.

## Practical example

**Execution context:** Local development or reading exercise. The walkthrough identifies filenames and the machine where each operation belongs.

```dockerfile
FROM php:8.4-cli
RUN apt-get update \
    && apt-get install -y --no-install-recommends git unzip \
    && rm -rf /var/lib/apt/lists/*
RUN docker-php-ext-install pdo_mysql
COPY --from=composer:2 /usr/bin/composer /usr/local/bin/composer
WORKDIR /app
CMD ["php", "artisan", "serve", "--host=0.0.0.0", "--port=8000"]
```

## Complete local Compose file

The passwords below are deliberately public lab values. Use them only for this disposable database, which has no published host port. They are not production credentials.

```yaml
services:
  app:
    build: .
    working_dir: /app
    volumes:
      - .:/app
    ports:
      - "127.0.0.1:8000:8000"
    environment:
      DB_CONNECTION: mysql
      DB_HOST: db
      DB_PORT: "3306"
      DB_DATABASE: lesson
      DB_USERNAME: lesson
      DB_PASSWORD: local-only-password
    depends_on:
      db:
        condition: service_healthy
  db:
    image: mariadb:11.4
    environment:
      MARIADB_DATABASE: lesson
      MARIADB_USER: lesson
      MARIADB_PASSWORD: local-only-password
      MARIADB_ROOT_PASSWORD: local-only-root-password
    volumes:
      - db-data:/var/lib/mysql
    healthcheck:
      test: ["CMD", "healthcheck.sh", "--connect", "--innodb_initialized"]
      interval: 5s
      timeout: 3s
      retries: 20
      start_period: 30s
volumes:
  db-data:
```

## Step-by-step walkthrough

1. In a disposable copy of your Laravel project, save the block as `Dockerfile`. The base is the command-line PHP variant; it is not an HTTP production server.

2. The package step installs git and unzip so Composer can fetch and extract dependencies, then removes apt index files to reduce the layer. The extension helper compiles the MySQL PDO driver. Copy Composer from its official image; WORKDIR makes subsequent commands run from `/app`. Add any other extensions required by your own lock file.

3. Save the complete Compose block below as `compose.yaml` in that same directory. The mount makes your project and its installed vendor directory visible inside the container.

4. If `.env` does not exist, copy `.env.example` to `.env` using `cp .env.example .env`. Run `docker compose build app`, then `docker compose run --rm --no-deps app composer install --no-interaction`.

5. Run `docker compose run --rm --no-deps app php artisan key:generate`. This writes the local application key; never rotate an existing production key casually. Run `docker compose up -d --wait`, then `docker compose exec app php artisan migrate`. This creates tables in the disposable lesson database.

6. Open `http://127.0.0.1:8000`. The development server listens on the container interface, while only host loopback is published.

## Expected result

The existing Laravel application responds; its database is reachable as db:3306. Package installation and migrations must succeed before database routes work.

## Practice

Add a harmless route in the disposable checkout and refresh. Success: the response changes without rebuilding the runtime image.

## Common errors and troubleshooting

Missing vendor/autoload.php: run Composer in the mounted project. Permission denied on storage: align host and container ownership; avoid blanket chmod 777. “Could not find driver”: rebuild after enabling pdo_mysql.

## Knowledge check

1. What provides PHP?
2. What provides source?
3. What creates vendor?
4. Why bind 0.0.0.0 inside?
5. Is artisan serve a production server?

> [!question]- Answers
> 1. The runtime image.
> 2. The bind mount.
> 3. Composer install.
> 4. To accept requests entering the container network interface.
> 5. No.

## Cleanup

`docker compose down` removes the lab containers and network but preserves db-data. Delete that volume only if you deliberately want to erase the local database. Keep .env untracked.

## Related lessons

- [[00 - Learning Roadmap|Full learning checklist]]
- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker questions]]
- [[02 - Docker Development/02 - PHP Composer and Node.js|Next in the roadmap: PHP Composer and Node.js]]

## Official references

- [File official documentation](https://docs.docker.com/reference/dockerfile/)
- [Compose official documentation](https://docs.docker.com/reference/compose-file/services/)
- [Laravel official documentation](https://laravel.com/docs/13.x/deployment)

- [Official PHP image build recipe](https://github.com/docker-library/php/blob/master/8.4/bookworm/cli/Dockerfile)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[Docker/02 - Fundamentals/Docker CLI Flags and Combinations\|Docker CLI Flags and Combinations]] | [[02 - Docker Development/02 - PHP Composer and Node.js\|PHP Composer and Node.js]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
