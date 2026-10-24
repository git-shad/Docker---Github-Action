---
title: Docker CLI Flags and Combinations
subject: Docker
level: fundamentals
tags:
  - docker
  - fundamentals
  - cli
  - flags
verified_on: 2026-10-24
---

# Docker CLI Flags and Combinations

Read a Docker command, explain each flag, and combine options for interactive work, background services, storage, and troubleshooting.

> [!info] Example status
> Official documentation was checked on 2026-10-24. The commands below were not executed while writing this lesson. Expected output is illustrative. This is a local learning lab; it does not publish images or configure a production service.

## Questions answered

- What is a Docker flag, and how do short and long options work?
- What do `-i`, `-t`, `-d`, `-it`, and `-dit` mean?
- Which flags can I combine, and which combinations conflict?
- How do I combine ports, environment variables, mounts, networks, and resource limits?
- Why does `-f` mean something different in `logs`, `ps`, `build`, and Compose?
- How are Docker client options different from daemon options?

## Prerequisites

- Read [[01 - Docker Fundamentals/02 - Images and Containers|Images and Containers]] and [[01 - Docker Fundamentals/07 - Docker Commands|Docker Commands]]. For storage and networking, revisit [[01 - Docker Fundamentals/05 - Docker Volumes|Docker Volumes]] and [[01 - Docker Fundamentals/06 - Docker Networks|Docker Networks]].
- Use Ubuntu Linux with **Bash**, an installed Docker CLI, and access to a local Docker Engine running Linux containers. Use Engine **28.0.0 or later** for the localhost port examples: older versions have a documented localhost-publishing exposure issue.
- The final optional build and Compose steps require the Buildx plugin and Compose **v2** plugin. They are not needed for the earlier `run` examples.
- Have permission to pull public images and create local practice resources. No registry account is needed for these public pulls. The first pull needs internet access and disk space.
- `alpine:3.22` and `nginx:stable-alpine` are readable teaching tags, not immutable release inputs. Tags can move. Review image support and use reviewed digests for reproducible releases.

On Windows, use a configured WSL2 Ubuntu Bash shell with Docker integration. On macOS, use Docker Desktop with Linux containers and Bash for these examples. Bind mounts must be accessible to Docker Desktop. These commands are not PowerShell syntax. A bind-mount source belongs to the daemon's machine, so the walkthrough assumes a local daemon.

## Explanation

### A flag changes one command's behavior

The **CLI** is Docker's command-line client. A **flag**, also called an option, changes what a command does. The client sends requests to the **daemon**, the background service that manages containers.

In `docker run --rm -it alpine:3.22 sh`, `docker` is the client, `run` is the command, `--rm` and `-it` are options, `alpine:3.22` is the image, and `sh` is the program to run inside the new container.

For `run`, the order is **Docker global options → `run` → run options → image → container command and its arguments**. For example:

```bash
docker --debug run --rm alpine:3.22 ls -l /etc/hostname
```

`--debug` enables client debugging. `--rm` belongs to `run`. After the image, `-l` belongs to `ls`, the program inside the container. Put `run` options before the image; putting `--name` after the image passes it to the container command instead.

`docker run` is an alias for `docker container run`. Their options have the same meanings.

### Short names, values, and grouping

| Form | Meaning | Example |
| --- | --- | --- |
| Short and long aliases | Two names for the same option on this command | `-d` and `--detach` for `run` |
| Boolean option | An on/off choice; a bare flag enables it | `--rm`, or explicitly `--rm=true` |
| Option with a value | The next word or text after `=` supplies its value | `--name flags-web` or `--name=flags-web` |
| Repeated option | Add multiple values when the command supports a list | `-e MODE=demo -e COLOR=blue` |
| Grouped short booleans | Combine supported short flags without separate values | `-it` means `-i -t`; `-dit` means `-d -i -t` |

Do not invent combined long options such as `--rmit`. Write `--rm -it`. Keep value-taking options such as `-p 127.0.0.1:8080:80` separate when learning so their arguments remain clear. Repeating a scalar such as `--name` does not create several names.

Flags belong to a particular command. `docker run -t` allocates a terminal; `docker build -t` assigns an image tag; `docker logs -t` adds timestamps. Uppercase and lowercase also differ: `run -p` publishes an explicit mapping, while `run -P` publishes exposed ports using automatically assigned host ports.

### Common `docker run` flags

| Flag | Meaning | When to use it |
| --- | --- | --- |
| `-i`, `--interactive` | Keep standard input open | Type into a process or pipe input into it. |
| `-t`, `--tty` | Allocate a pseudo-terminal, a terminal interface for the process | Interactive terminal applications; usually paired with `-i`. |
| `-d`, `--detach` | Start in the background and print the container ID | A service whose main process keeps running. |
| `--rm` | Remove the container's writable layer and associated anonymous volumes when it exits | Disposable tasks. It preserves the image, named volumes, and bind-mounted host files. |
| `--name` | Assign a container name | Refer to the container in later commands. Names must be available on that daemon. |
| `-p`, `--publish` | Map a host address and port to a container port | Reach a service from the host. Omitting the host IP normally publishes on all host interfaces. |
| `-e`, `--env`; `--env-file` | Set container environment variables directly or from a file | Non-secret runtime settings. Environment values can be inspected; use an appropriate secret mechanism for credentials. |
| `-v`, `--volume`; `--mount` | Attach storage | Named volumes preserve data; bind mounts expose selected host paths. This lesson uses explicit `--mount` fields. |
| `--network` | Select a container network | Communicate with other containers on the intended network. |
| `--restart` | Select an automatic restart policy | Long-running services. Policies include `no`, `on-failure[:N]`, `always`, and `unless-stopped`. |
| `-u`, `--user` | Choose the process user or numeric UID, optionally with a group | Run with the intended file permissions. The image must support that user. |
| `--read-only` | Make the container's root filesystem read-only | Programs that do not need to change image files; separate writable mounts can still be used. |
| `--tmpfs` | Add a temporary filesystem mount | Give a read-only container a disposable writable directory, such as `/tmp`. |
| `-m`, `--memory`; `--cpus` | Limit memory and CPU time | Bound a practice workload. Kernel and rootless support can affect enforcement. |
| `--init` | Add a small init process to forward signals and reap exited child processes | Programs that launch child processes. |

`-d` does not keep an exited program alive. A container stops when its main process exits. A restart policy handles process exit; an unhealthy health check alone does not make Docker restart a container. `--memory` also has separate swap behavior; it is not a complete memory-and-swap policy by itself.

### Useful combinations and their limits

These are option patterns, not standalone commands. The walkthrough supplies complete commands and creates their prerequisites. This is a practical selection, not an enumeration of every possible permutation.

| Combination | Combined meaning | Appropriate use or limitation |
| --- | --- | --- |
| `-it` | Input stays open and a terminal is allocated | Work in an interactive shell. It does not remove the container afterward. |
| `--rm -it` | Interactive terminal plus automatic cleanup on exit | A disposable shell. Save needed files outside the writable layer first. |
| `--rm -i` | Accept piped input and remove the container after processing | Shell pipelines and redirected input; leave off `-t` when there is no terminal. |
| `-dit` | Background mode, open input, and a terminal | Keep a shell available for a later attachment. Many services need only `-d`. |
| `-d --rm` | Background execution with cleanup on process exit | A temporary background job. Capture needed logs before it exits. |
| `-d --name flags-web -p 127.0.0.1:8080:80` | Named background service published on host localhost | Local web development with the default bridge networking used here. |
| `--rm -e MODE=demo --mount "type=bind,src=$flags_lab_dir,dst=/work,readonly"` | Temporary process with a setting and a read-only host mount | Read a chosen local file after the walkthrough creates the source directory. |
| `--rm --network flags-net --mount type=volume,src=flags-data,dst=/data` | Temporary container using a network and persistent named storage | Remove the container while retaining the named volume. Create/select those resources first. |
| `--read-only --tmpfs /tmp` | Read-only root filesystem with temporary writable space | Programs that write only to the explicitly writable location. |
| `--memory 128m --cpus 0.5` | Memory ceiling and CPU-time quota | A bounded local task; this does not pin the process to one particular CPU core. |
| `-d --restart unless-stopped` | Background service that restarts unless explicitly stopped | Retain the container for service operation. |
| `--rm --restart always` | Conflicting lifecycle requests | Invalid: removal and an automatic restart policy cannot be combined. |

Also avoid `-t` in non-terminal automation or pipelines, and avoid combining `-d` with `--attach`: detached and attached operation conflict. Publishing ports with host networking is not the bridge port-mapping model used here; choose the network model deliberately.

### Global client flags and daemon flags

`docker --context CONTEXT_NAME ...` selects a saved connection for one command. `docker --host ENDPOINT ...` selects an endpoint directly. Use one connection selector at a time. First inspect `docker context ls`; do not assume a name means a local engine. A temporary `--context` selection does not change the saved active context.

Client `--config` selects a client configuration **directory**, commonly `~/.docker`. Daemon `dockerd --config-file` selects a daemon configuration **file**. `docker --debug` logs client activity; `dockerd --debug` logs daemon activity. See [[Docker/04 - Advanced/Docker Daemon Flags and Configuration|Docker Daemon Flags and Configuration]] for the service settings.

## Walkthrough

Run these steps in one **local Bash session**. Each command acts on the selected Docker daemon. Use the `flags-` names only if they are unused; if they belong to an existing project, choose a different prefix consistently. Reading this note creates no resources.

### 1. Inspect the target and command help

```bash
docker version
docker context show
docker context ls
docker ps -a --filter name=flags-
docker run --help
```

Check the server version and context endpoint before continuing. `--help` describes the installed client's options; daemon/API support can still differ. If the server cannot be reached, fix that connection before the run steps.

Create a temporary directory for this lesson's local files. Its path is saved in a task-specific variable; keep this terminal open until cleanup.

```bash
flags_lab_dir="$(mktemp -d -t docker-cli-flags.XXXXXX)"
printf 'hello from the host\n' > "$flags_lab_dir/message.txt"
```

### 2. Compare an interactive terminal with piped input

The next command can pull Alpine. It starts a disposable shell; exiting deletes that container and its writable layer.

```bash
docker run --rm -it --name flags-shell alpine:3.22 sh
```

At the **container's shell prompt**, run:

```bash
cat /etc/alpine-release
exit
```

Back in the **host shell**, send a line through standard input without allocating a terminal:

```bash
printf 'hello through stdin\n' | docker run --rm -i alpine:3.22 cat
```

`-i` keeps the input stream open; `cat` reads that stream. `-t` is unnecessary for a pipe and can produce a TTY error when no terminal is available.

### 3. Run and inspect a background web service

This command may pull Nginx and opens host **localhost port 8080**. The mapping is host IP `127.0.0.1`, host port `8080`, container port `80`, using TCP. It assumes the default bridge network and Engine 28 or later.

```bash
docker run -d --name flags-web -p 127.0.0.1:8080:80 nginx:stable-alpine
docker ps --filter name=flags-web
docker port flags-web
docker exec flags-web nginx -t
docker logs --tail 10 --timestamps flags-web
```

Open `http://127.0.0.1:8080` in a browser on the Docker host. Nginx may need a moment to start. Its default command runs the service in the foreground **inside** the container; `-d` detaches the Docker client from it.

To stream recent logs:

```bash
docker logs -f --tail 10 --since 5m flags-web
```

Press **Ctrl+C** to end log following. This ends the log client; the web container continues running. Logs may contain application data, so review them before sharing.

### 4. Understand `-dit`, `exec`, and a temporary background job

```bash
docker run -dit --name flags-detached alpine:3.22 sh
docker exec -it flags-detached sh
```

Inside this second shell, run `exit`. That ends the process started by `exec`; the original detached shell remains. `docker attach flags-detached` instead connects to the original process: **Ctrl+P, then Ctrl+Q** detaches from that interactive session; `exit` terminates its main shell and stops the container.

For a background job that cleans itself up after about five seconds:

```bash
docker run -d --rm --name flags-once alpine:3.22 sleep 5
docker ps -a --filter name=flags-once
```

The result depends on when you inspect it. While running it can appear; after it exits, automatic removal means it no longer appears. An empty result does not prove that an arbitrary application succeeded.

### 5. Combine environment variables with a read-only bind mount

The bind mount below gives the container read access to the selected host directory. It exposes only the lesson files in that directory, not your home directory. `readonly` prevents writing through this mount.

```bash
docker run --rm \
  -e MODE=practice \
  --mount "type=bind,src=$flags_lab_dir,dst=/work,readonly" \
  alpine:3.22 sh -c 'printf "mode=%s\n" "$MODE"; cat /work/message.txt'
```

The host expands `$flags_lab_dir` inside double quotes. Single quotes around the `sh -c` program preserve `$MODE` for the container's shell to expand. Here the semicolon belongs to that shell program, after the image. Use non-secret values for environment examples.

### 6. Combine named storage and a user-defined network

These commands create a network and a named volume, then write one lesson file into the volume. Use them only if `flags-net` and `flags-data` are available for this lab; `docker volume create` can select an existing volume with the same name.

```bash
docker network create flags-net
docker volume create flags-data
docker run --rm \
  --network flags-net \
  --mount type=volume,src=flags-data,dst=/data \
  alpine:3.22 sh -c 'printf "saved in a named volume\n" > /data/note.txt'
docker run --rm \
  --network flags-net \
  --mount type=volume,src=flags-data,dst=/data,readonly \
  alpine:3.22 cat /data/note.txt
```

The first container is removed, but the second can still read its file because the named volume remains. Selecting the network does not publish any ports. This small example combines independent options; [[01 - Docker Fundamentals/06 - Docker Networks|Docker Networks]] demonstrates communication between containers.

### 7. Add filesystem and resource restrictions

```bash
docker run --rm --read-only --tmpfs /tmp \
  --user 1000:1000 --memory 128m --cpus 0.5 \
  alpine:3.22 sh -c 'id; printf "temporary\n" > /tmp/note.txt; cat /tmp/note.txt'
```

The numeric UID/GID selects the process identity. `/tmp` is an explicitly writable temporary mount, and the root filesystem stays read-only. The mount disappears after exit. Resource flags request limits; inspect kernel/rootless support rather than inferring enforcement from a successful print command. This demonstration is not a complete security configuration.

### 8. Optional: compare build flags with run flags

Use the same host Bash session. The following creates **`Dockerfile`** and **`index.html`** inside the temporary lab directory, then builds a local image. The build can download its base image and consumes disk space. It does not push anything to a registry.

```bash
cat > "$flags_lab_dir/Dockerfile" <<'DOCKERFILE'
FROM nginx:stable-alpine
COPY index.html /usr/share/nginx/html/index.html
DOCKERFILE
printf '<h1>flag combinations</h1>\n' > "$flags_lab_dir/index.html"
docker buildx version
docker build --pull --no-cache \
  -f "$flags_lab_dir/Dockerfile" \
  -t flags-web:lesson "$flags_lab_dir"
docker run --rm flags-web:lesson nginx -t
```

`-f` selects a Dockerfile; `-t` assigns an image tag; the final directory is the build context. `--no-cache` disables reuse of build layers for this build, while `--pull` requests fresh base-image resolution. Neither option publishes the result. Ordinary builds can reuse caching; these flags are included to explain their separate roles.

This uses `docker build` with its default local builder. If you deliberately switch to `docker buildx build` with a non-default builder, select an output such as `--load` before expecting to use the image locally.

### 9. Optional: compare Compose flags

After step 8, create **`compose.yaml`** in the same directory:

```bash
cat > "$flags_lab_dir/compose.yaml" <<'YAML'
services:
  web:
    build: .
    ports:
      - "127.0.0.1:8081:80"
YAML
docker compose version
docker compose -p flags-compose -f "$flags_lab_dir/compose.yaml" config
```

`config` validates and renders the Compose model without starting it. The next command builds and starts its web service, publishing **localhost port 8081**. Keep the project name dedicated to this lab.

```bash
docker compose -p flags-compose -f "$flags_lab_dir/compose.yaml" up -d --build
docker compose -p flags-compose -f "$flags_lab_dir/compose.yaml" ps
```

Open `http://127.0.0.1:8081` after startup. Compose's `-p` means **project name**, and its `-f` selects a Compose file. `up -d` detaches from the services, while `--build` builds service images before startup. `--build` does not mean the same thing as `--no-cache`.

### 10. Read reused short flags by command

| Command from this lesson | Meaning of its flags |
| --- | --- |
| `docker ps -a -q --filter name=flags-web` | Include stopped containers, print IDs only, and filter the selection. The compact boolean form is `-aq`. |
| `docker logs -f --tail 10 flags-web` | Follow logs, starting with the last ten lines. |
| `docker exec -it flags-detached sh` | Start another shell in a running container with input and a terminal. |
| `docker build -f Dockerfile -t flags-web:lesson .` | In the lab directory, choose the recipe, assign a tag, and use that directory as context. |
| `docker compose -p flags-compose -f compose.yaml up -d --build` | In the lab directory, select a project/file, build, and start detached. |

Ask for help at the right level: `docker --help`, `docker run --help`, `docker logs --help`, or `docker compose up --help`.

## Expected result

- The interactive shell accepts input; exiting removes `flags-shell`.
- The pipe prints `hello through stdin`.
- The first web service becomes reachable at localhost port 8080; the optional Compose service serves `flag combinations` at localhost port 8081.
- The environment/mount example prints the selected mode and host file contents.
- The named-volume reader finds the file written by an already-removed container.
- The restricted process reports UID/GID 1000 and can write to `/tmp`.

Example output from step 5, **not captured execution evidence**:

```text
mode=practice
hello from the host
```

## Practice

Create a small clock service with a restart policy. This creates a persistent container named `flags-clock`; it does not publish a port.

```bash
docker run -d --name flags-clock --restart unless-stopped \
  alpine:3.22 sh -c 'while true; do date; sleep 5; done'
docker logs --tail 3 flags-clock
docker inspect --format '{{.HostConfig.RestartPolicy.Name}}' flags-clock
docker stop flags-clock
docker ps -a --filter name=flags-clock
```

**Success condition:** it produces timestamps, inspection reports `unless-stopped`, and manual stopping leaves an exited container available for inspection. Explain why adding `--rm` would conflict, and why `-t` is unnecessary for this service.

## Troubleshooting

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| Unknown flag | Wrong command scope, spelling, or client version | Check that command's `--help`; check `docker version` for client/server differences. |
| Option appears as a program error | Docker option placed after the image | Move `run` options before the image; retain program options after it. |
| Input device is not a TTY | `-t` used with redirected/non-terminal input | Use `-i` for a pipeline or an appropriate non-interactive command. |
| Detached container immediately exits | Main process finished or failed | Use `ps -a`, logs, and inspection. During diagnosis, preserve the container by leaving off `--rm`. |
| Container name is already in use | A running or stopped container owns it | Inspect that container and use a new lab name. Do not delete an unrelated resource. |
| Port is already allocated | Another service uses the selected host port | Use an unused host port, such as `127.0.0.1:8082:80`. |
| Bind source does not exist | Wrong host path, wrong daemon, or closed/replaced shell variable | Confirm the lab directory exists on the daemon's host; review context and Desktop sharing. |
| Read-only filesystem / permission denied | Program writes outside allowed mounts or selected UID lacks access | Choose an intended writable mount and compatible image/user permissions. |
| `--rm` and restart policy error | Automatic cleanup conflicts with restarting | Choose disposable execution or a retained container with a restart policy. |
| CPU/memory option unsupported | Missing kernel or rootless capabilities | Read `docker info` warnings and the resource-constraints documentation. |
| New build image unavailable locally | A chosen Buildx builder did not load its output | Use the default `docker build` flow here, or explicitly select local output with `--load` when using Buildx. |

## Knowledge check

1. Is `-it` a separate Docker feature, or a combination of flags?
2. Does `--rm` delete an explicitly named volume?
3. Does `-d` keep a completed program running?
4. Does `-p` always mean publish a port?
5. Why does piped input normally use `-i` without `-t`?

> [!question]- Answers
> 1. It combines `-i` and `-t`: input remains open and a terminal is allocated.
> 2. No. It removes the container and associated anonymous volumes; explicitly named volumes remain.
> 3. No. Container lifetime follows its main process.
> 4. No. `docker run -p` publishes ports, while `docker compose -p` chooses the project name.
> 5. A pipe carries input without providing an interactive terminal.

## Cleanup

Preserve needed logs/files first. Run only the cleanup for steps you completed, on the same daemon and with the lab names you selected.

If you completed the Compose step, this stops and removes **this lesson's** Compose containers and network. It leaves images and named volumes in place; this example defines no volumes.

```bash
docker compose -p flags-compose -f "$flags_lab_dir/compose.yaml" down
```

For standalone containers, the following stops and removes only matching lesson names that still exist. Removal deletes their writable layers. Do not use this list if those names were already in use before your lab.

```bash
for flags_container in flags-web flags-detached flags-once flags-clock; do
  if docker container inspect "$flags_container" >/dev/null 2>&1; then
    docker stop "$flags_container"
    if docker container inspect "$flags_container" >/dev/null 2>&1; then
      docker rm "$flags_container"
    fi
  fi
done
```

If step 6 created the named volume and network exclusively for the lesson, these commands **permanently delete the saved volume file** and remove the lab network:

```bash
docker volume rm flags-data
docker network rm flags-net
```

If built in steps 8–9, optionally remove the local tags `flags-web:lesson` and `flags-compose-web` with `docker image rm flags-web:lesson` and `docker image rm flags-compose-web`. Image removal can fail if another container uses them; inspect that usage rather than forcing it. Leave shared downloaded base images and build caches in place unless you separately choose to manage them.

Finally, delete only the known lesson files and remove the empty temporary directory. `rmdir` refuses to remove a directory that still contains other files.

```bash
if [ -n "${flags_lab_dir:-}" ] && [ -d "$flags_lab_dir" ]; then
  rm -- "$flags_lab_dir/message.txt"
  for flags_file in Dockerfile index.html compose.yaml; do
    if [ -f "$flags_lab_dir/$flags_file" ]; then
      rm -- "$flags_lab_dir/$flags_file"
    fi
  done
  rmdir -- "$flags_lab_dir"
fi
```

## Related lessons

- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[01 - Docker Fundamentals/02 - Images and Containers|Images and Containers]]
- [[01 - Docker Fundamentals/07 - Docker Commands|Docker Commands]]
- [[01 - Docker Fundamentals/05 - Docker Volumes|Docker Volumes]]
- [[01 - Docker Fundamentals/06 - Docker Networks|Docker Networks]]
- [[07 - References/01 - Docker Command Cheat Sheet|Docker Command Cheat Sheet]]
- [[Docker/04 - Advanced/Docker Daemon Flags and Configuration|Advanced extension: Docker Daemon Flags and Configuration]]
- [[02 - Docker Development/01 - Laravel Docker Environment|Next in the roadmap: Laravel Docker Environment]]

## References

- [Docker CLI: option types, help, and client configuration](https://docs.docker.com/reference/cli/docker/)
- [Container run: flags and lifecycle combinations](https://docs.docker.com/reference/cli/docker/container/run/)
- [Container exec](https://docs.docker.com/reference/cli/docker/container/exec/)
- [Container logs](https://docs.docker.com/reference/cli/docker/container/logs/)
- [Container listing and filters](https://docs.docker.com/reference/cli/docker/container/ls/)
- [Buildx build: current build options](https://docs.docker.com/reference/cli/docker/buildx/build/)
- [Compose command options](https://docs.docker.com/reference/cli/docker/compose/)
- [Compose up](https://docs.docker.com/reference/cli/docker/compose/up/)
- [Port publishing and localhost version caveat](https://docs.docker.com/engine/network/port-publishing/)
- [Resource constraints](https://docs.docker.com/engine/containers/resource_constraints/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[01 - Docker Fundamentals/07 - Docker Commands\|Docker Commands]] | [[02 - Docker Development/01 - Laravel Docker Environment\|Laravel Docker Environment]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
