# Docker questions

[[Home|Home]] · [[Docker/Docker Index|Ordered learning path]]

Search this note for your question, then open its linked tutorial.

## 01 - Docker Fundamentals

- [[01 - Docker Fundamentals/01 - Introduction to Docker|What does Docker solve, and how do I run my first container?]]
- [[01 - Docker Fundamentals/02 - Images and Containers|What is the difference between an image and a container?]]
- [[01 - Docker Fundamentals/03 - Dockerfile Instructions|How do RUN, CMD, ENTRYPOINT, and COPY differ?]]
- [[01 - Docker Fundamentals/04 - Docker Compose|How does Compose describe and start multiple services?]]
- [[01 - Docker Fundamentals/05 - Docker Volumes|How does a volume preserve data after a container is removed?]]
- [[01 - Docker Fundamentals/06 - Docker Networks|How do containers find each other without publishing ports?]]
- [[01 - Docker Fundamentals/07 - Docker Commands|Which Docker commands do I use to inspect and clean up a container?]]
- [[Docker/02 - Fundamentals/Docker CLI Flags and Combinations|What do -i, -t, -d, -it, and -dit mean?]]
- [[Docker/02 - Fundamentals/Docker CLI Flags and Combinations|Which Docker flags can I combine, and which combinations conflict?]]
- [[Docker/02 - Fundamentals/Docker CLI Flags and Combinations|How do I combine ports, environment variables, mounts, and resource limits?]]
- [[Docker/02 - Fundamentals/Docker CLI Flags and Combinations|Why do -f, -p, and -t mean different things in different Docker commands?]]

## 02 - Docker Development

- [[02 - Docker Development/01 - Laravel Docker Environment|How do I run an existing Laravel application in Docker?]]
- [[02 - Docker Development/02 - PHP Composer and Node.js|Why do Laravel projects need Composer and sometimes Node.js?]]
- [[02 - Docker Development/03 - MariaDB Container|How do I start MariaDB with a persistent data directory?]]
- [[02 - Docker Development/04 - Laravel and MariaDB Networking|Why must Laravel use DB_HOST=db instead of localhost?]]
- [[02 - Docker Development/05 - Environment Variables|How do image ENV, Compose .env, and Laravel .env differ?]]
- [[02 - Docker Development/06 - Bind Mounts and Hot Reload|How do bind mounts and hot reload work together?]]
- [[02 - Docker Development/07 - Multi-stage Docker Builds|How do multi-stage builds keep Node.js out of the final web image?]]
- [[02 - Docker Development/08 - Docker Health Checks|How does Docker distinguish running from healthy?]]
- [[02 - Docker Development/09 - Development vs Production|What must change before a development container can serve production?]]

## 07 - References

- [[07 - References/01 - Docker Command Cheat Sheet|Which Docker commands should I reach for during daily development?]]
- [[07 - References/02 - Dockerfile Instruction Reference|What do the main Dockerfile instructions mean?]]
- [[07 - References/03 - Docker Compose YAML Reference|What do services, ports, volumes, networks, and depends_on mean?]]
- [[07 - References/05 - DevOps Glossary|What do the common Docker and CI/CD terms mean?]]

## Advanced extensions

- [[Docker/04 - Advanced/Docker Daemon Flags and Configuration|How do docker client flags differ from dockerd Engine flags?]]
- [[Docker/04 - Advanced/Docker Daemon Flags and Configuration|How do I combine daemon startup flags with daemon.json without conflicts?]]
- [[Docker/04 - Advanced/Docker Daemon Flags and Configuration|How do I validate Docker daemon configuration without starting another daemon?]]
- [[Docker/04 - Advanced/Docker Daemon Flags and Configuration|How do I apply, verify, and roll back a Docker Engine configuration change?]]
- [[Docker/04 - Advanced/Docker Daemon Flags and Configuration|Where is daemon configuration stored for Linux, rootless Docker, Windows, and Docker Desktop?]]
- How do I optimize BuildKit caches without leaking secrets? (planned)
- How do I implement zero-downtime schema changes? (planned)

## Examples

- [[Docker/Examples/Docker Examples|Browse Docker examples]]

## Dockerfile and Compose mastery

- [[08 - Dockerfile and Compose Mastery/01 - Dockerfile Syntax Fundamentals|How do I write a Dockerfile from an empty file?]]
- [[08 - Dockerfile and Compose Mastery/02 - Dockerfile Instructions Reference|What exactly does each important Dockerfile instruction accept and change?]]
- [[08 - Dockerfile and Compose Mastery/03 - FROM RUN CMD and ENTRYPOINT|How do FROM, RUN, CMD, and ENTRYPOINT interact?]]
- [[08 - Dockerfile and Compose Mastery/04 - COPY ADD WORKDIR and USER|When should I use COPY, ADD, WORKDIR, and USER?]]
- [[08 - Dockerfile and Compose Mastery/05 - ARG ENV EXPOSE and VOLUME|How do ARG, ENV, EXPOSE, and VOLUME differ?]]
- [[08 - Dockerfile and Compose Mastery/06 - Build Context and Dockerignore|Why can COPY fail when a file exists near my Dockerfile?]]
- [[08 - Dockerfile and Compose Mastery/07 - Docker Layer Caching|How do build layers and cache mounts affect my recipe?]]
- [[08 - Dockerfile and Compose Mastery/08 - Multi-stage Builds|How do I design a multi-stage frontend build?]]
- [[08 - Dockerfile and Compose Mastery/09 - Dockerfile Best Practices|What makes a Dockerfile reliable rather than merely short?]]
- [[08 - Dockerfile and Compose Mastery/10 - Dockerfile Anti-patterns|Which Dockerfile anti-patterns should I recognize?]]
- [[08 - Dockerfile and Compose Mastery/11 - Compose YAML Syntax Fundamentals|How do indentation, data types, anchors, and extension fields work in compose.yml?]]
- [[08 - Dockerfile and Compose Mastery/12 - Compose Services and Images|What do Compose services mean and where does each important property belong?]]
- [[08 - Dockerfile and Compose Mastery/13 - Compose Build and Dockerfile Integration|How does Compose choose a Dockerfile and pass non-secret build arguments?]]
- [[08 - Dockerfile and Compose Mastery/14 - Compose Ports and Networks|How should I connect services without exposing every port?]]
- [[08 - Dockerfile and Compose Mastery/15 - Compose Volumes and Bind Mounts|When should I use a named volume rather than a bind mount?]]
- [[08 - Dockerfile and Compose Mastery/16 - Compose Environment Variables|How do .env, env_file, environment, and dollar escaping differ?]]
- [[08 - Dockerfile and Compose Mastery/17 - Compose Depends On and Healthchecks|What do depends_on and healthcheck actually guarantee?]]
- [[08 - Dockerfile and Compose Mastery/18 - Compose Commands and Entrypoints|How do Compose command and entrypoint differ from Dockerfile CMD and ENTRYPOINT?]]
- [[08 - Dockerfile and Compose Mastery/19 - Compose Profiles and Overrides|How do profiles and multiple Compose files change the final model?]]
- [[08 - Dockerfile and Compose Mastery/20 - Compose Best Practices|What should a maintainable Compose configuration make explicit?]]
- [[08 - Dockerfile and Compose Mastery/21 - Compose Anti-patterns|Which Compose anti-patterns should I avoid?]]
- [[08 - Dockerfile and Compose Mastery/22 - Dockerfile vs Compose|What belongs in Dockerfile and what belongs in compose.yml?]]
- [[08 - Dockerfile and Compose Mastery/23 - Dockerfile and Compose Together|How do I write Dockerfile and compose.yml together from scratch?]]
- [[08 - Dockerfile and Compose Mastery/24 - Laravel React MariaDB Example|How can I combine a PHP backend, compiled frontend, and persistent database?]]
- [[08 - Dockerfile and Compose Mastery/25 - Development vs Production|How should development builds differ from production builds?]]
- [[08 - Dockerfile and Compose Mastery/26 - Debugging Docker Configurations|How do I debug a Dockerfile or Compose model safely?]]
- [[08 - Dockerfile and Compose Mastery/27 - Security and Secrets|How do I avoid embedding credentials in images or tracked Compose files?]]
- [[08 - Dockerfile and Compose Mastery/28 - Practice Exercises|How can I practice writing Dockerfiles and Compose files rather than only reading examples?]]
- [[08 - Dockerfile and Compose Mastery/29 - Common Errors and Solutions|How do I fix common Docker configuration errors without guessing?]]
- [[08 - Dockerfile and Compose Mastery/30 - Final Assessment|Can I write, explain, and review a Dockerfile and Compose model without copying blindly?]]
