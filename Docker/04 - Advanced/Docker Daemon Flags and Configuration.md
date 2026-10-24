---
title: Docker Daemon Flags and Configuration
subject: Docker
level: advanced
tags:
  - docker
  - advanced
  - engine
  - daemon
  - flags
verified_on: 2026-10-24
---

# Docker Daemon Flags and Configuration

Explain Docker Engine startup flags, combine them with a configuration file, and validate a candidate before applying a reversible change in a disposable Linux VM.

> [!info] Example status
> Official documentation was checked on 2026-10-24. These examples were not executed while writing the lesson. Expected results are illustrative. The first walkthrough validates temporary files; the optional VM exercise changes a service and starts a test container only if you choose to perform it later.

## Questions answered

- How are `docker` client flags different from `dockerd` daemon flags?
- What do common Engine flags mean, and which options work together?
- How do I translate daemon flags into `daemon.json`?
- What happens if a setting appears in both the startup flags and the JSON file?
- How can I validate configuration without starting another daemon?
- How do I apply, verify, and roll back a daemon configuration change?
- What differs between rootful Linux, rootless Docker, Windows Engine, and Docker Desktop?

## Prerequisites

- Read [[Docker/02 - Fundamentals/Docker CLI Flags and Combinations|Docker CLI Flags and Combinations]] and [[01 - Docker Fundamentals/07 - Docker Commands|Docker Commands]]. Review [[01 - Docker Fundamentals/05 - Docker Volumes|Docker Volumes]], [[01 - Docker Fundamentals/06 - Docker Networks|Docker Networks]], and [[08 - Dockerfile and Compose Mastery/27 - Security and Secrets|Security and Secrets]].
- Use **Bash on Ubuntu Linux**, Docker Engine **28 or later**, and an installed `dockerd` binary. Check the installed version's help: options can depend on the Engine release, operating system, and storage backend.
- The optional service exercise requires a **disposable rootful Ubuntu VM using systemd**, administrator access through `sudo`, and **Python 3** to merge JSON while preserving unrelated settings. Its default local socket must be `/var/run/docker.sock`. Have console access and a VM snapshot before service changes.
- The optional verification container uses public `alpine:3.22`, so a first pull needs internet access. Its tag is a mutable teaching default, not a release guarantee.

Use a VM with no valuable workloads. Restarting Docker can interrupt containers and client connections; do not assume live restore eliminates disruption. The service exercise keeps the existing data directory and local socket configuration. Storage migration and remote access provisioning require their own reviewed procedures.

For Docker Desktop on macOS, Windows, or Linux, read the platform table below. Do not apply this rootful systemd walkthrough to the host merely because the Docker CLI is available in a terminal or WSL shell. Rootless service management also follows a different procedure.

## Explanation

### Client request versus daemon startup

`docker` is the **client** that issues requests. `dockerd` is the persistent **daemon** that manages images, containers, networks, and volumes. The daemon is part of Docker Engine and normally runs under a service manager such as systemd on Ubuntu.

| Command or file | Controls | Example meaning |
| --- | --- | --- |
| `docker --debug ...` | This client's behavior | Show debugging information for the client request. |
| `dockerd --debug` | Daemon startup behavior | Enable service debugging when a daemon starts. This invocation would start a daemon; it is not a client request. |
| `docker --host ...` / `docker --context ...` | Which daemon the client contacts | Connect this request to an endpoint or saved context. |
| `dockerd --host ...` | Where the daemon listens | Configure service sockets; it does not select a remote daemon to manage. |
| Client `--config` and `~/.docker/config.json` | Client configuration | Client preferences and registry-related configuration. `--config` selects a directory. |
| Daemon `--config-file` and `daemon.json` | Engine configuration | Service settings. `--config-file` selects a file. |
| `docker run --log-driver ...` | One new container | Override its logging driver without changing the daemon-wide default. |

Typing `dockerd --debug` does not turn on debugging for an already-running service. It tries to start another daemon, which can collide with the running daemon's socket, PID file, or storage. Use validation for the file exercises below; change a managed service through its actual configuration and service manager.

### Common daemon flags and JSON equivalents

The values in this table illustrate the option format. Names such as `DNS_IP`, `PATH`, and `FILE` are placeholders, not commands to paste. The walkthrough supplies complete local examples.

| Startup option | `daemon.json` equivalent | Meaning and limits |
| --- | --- | --- |
| `-D`, `--debug` | `"debug": true` | More detailed daemon diagnostics. Debug logs can be noisy and contain operational data. |
| `-l`, `--log-level=info` | `"log-level": "info"` | Daemon logging level; this is distinct from a container logging driver. |
| `--log-driver=json-file` | `"log-driver": "json-file"` | Default logging driver for newly created containers. |
| `--log-opt=max-size=10m` and `--log-opt=max-file=3` | `"log-opts": {"max-size": "10m", "max-file": "3"}` | Driver-specific options; these enable rotation for `json-file`. JSON log-option values must be strings. |
| `--dns=DNS_IP` | `"dns": ["DNS_IP"]` | Default DNS server list for containers; it must point to reachable, appropriate resolvers. |
| `--data-root=PATH` | `"data-root": "PATH"` | Directory for Docker-managed state; see the storage backend caveat below. This does not migrate existing data. |
| `-H`, `--host=ENDPOINT` | `"hosts": ["ENDPOINT"]` | Listening endpoints. Multiple `--host` flags define a list. Changing these affects access to the Engine API. |
| `--tlsverify` | `"tlsverify": true` | Enable TLS with peer verification; server/client certificates and trust must also be configured. |
| `--tlscacert=FILE` | `"tlscacert": "FILE"` | Trusted certificate authority file for TLS. |
| `--tlscert=FILE`, `--tlskey=FILE` | `"tlscert": "FILE"`, `"tlskey": "FILE"` | Daemon certificate and private key. Protect the key as a credential. |
| `--live-restore` | `"live-restore": true` | Support keeping containers running across some daemon outages; it has documented limitations and is not a general uptime guarantee. |
| `--config-file=FILE` | No JSON setting for selecting itself | Read configuration from the selected file. |
| `--validate` | No JSON setting | Validate configuration and exit without starting the daemon. |

Some JSON keys use plural names, such as `hosts`, `log-opts`, and `default-address-pools`. Do not assume a flag can be copied verbatim into JSON. Use the installed version's reference. JSON allows no comments or trailing commas.

### Valid combinations and conflicts

| Combination | Meaning | Validity or condition |
| --- | --- | --- |
| `--log-driver=json-file` plus repeated `--log-opt` values | Select the driver and its rotation options | Valid when those settings are not also specified in the configuration file. |
| `--config-file=FILE --validate` | Check a chosen file and exit | Valid; no second daemon starts. |
| `--config-file=FILE --debug` | Read file settings and add daemon debugging | Valid only if the file omits `debug`. |
| `--debug` plus file `"debug": true` | Specify the same option in two places | Invalid even though the values agree. Neither source overrides the duplicate. |
| `--log-driver=json-file` plus file `"log-driver": "json-file"` | Repeat the logging-driver selection across sources | Invalid for the same reason. |
| Repeated `--dns` flags | Supply multiple default resolvers | Valid list syntax when addresses are suitable; do not also supply the `dns` key in the file. |
| A Unix listener plus a TLS-protected TCP listener | Allow local and authenticated remote API requests | Requires a deliberate listener configuration, correct certificates, trust, firewall rules, and access control. It is not provisioned by this walkthrough. |
| systemd `-H fd://` plus file `"hosts": [...]` | Two sources configure the daemon's listeners | Conflicts and can prevent startup. Inspect `ExecStart` before adding `hosts`. |

List values from flags and JSON are not automatically merged across sources. Choose one source for each setting. A JSON file is generally easier to maintain; existing service-unit flags must still be considered.

The `json-file` driver stores container stdout/stderr, while `--debug` and `--log-level` concern the daemon's own diagnostics. Changing daemon logging defaults affects **newly created** containers; restarting an existing container does not automatically replace its saved logging configuration.

### Platform and version boundaries

| Setup | Configuration and service handling |
| --- | --- |
| Rootful Linux Engine | Default file `/etc/docker/daemon.json`; typically `sudo systemctl ... docker.service` on Ubuntu. Inspect unit flags and drop-ins first. |
| Rootless Linux Engine | Default file `~/.config/docker/daemon.json`, or `$XDG_CONFIG_HOME/docker/daemon.json` when configured; use `systemctl --user` for its user service. Direct startup uses `dockerd-rootless.sh`. |
| Windows Engine | Default file `C:\ProgramData\docker\config\daemon.json`; service management and supported options differ. The Bash/systemd commands below do not apply. |
| Docker Desktop | Use **Settings → Docker Engine** for supported daemon JSON and **Apply & restart**. The engine runs in Desktop's managed environment. Host `/etc/docker/daemon.json` is not automatically its configuration. |

With the **containerd image store**, image contents and container snapshots can be stored separately under `/var/lib/containerd`; changing `data-root` does not move that store. Fresh Engine 29 installations use the containerd image store by default, while upgraded installations can retain classic storage. Inspect the actual backend before planning a migration. Do not point two daemons at the same storage directory.

For remote access, a Unix socket is the normal local interface; SSH can carry remote Docker requests without opening a new TCP API listener. Direct TCP access needs authenticated TLS and suitable network restrictions. `--tls` alone on the daemon does not authenticate clients. The lab leaves listeners unchanged; follow [Protect the Docker daemon socket](https://docs.docker.com/engine/security/protect-access/) before configuring a remote endpoint.

## Walkthrough

Steps 1–4 use temporary configuration files on a machine where `dockerd` is installed. They do not edit a service or start another daemon. Use one host Bash session so its lab variables remain available. Stop and investigate if a command fails unexpectedly.

### 1. Inspect the installed tools and the target

```bash
dockerd --version
dockerd --help
docker version
docker context show
docker context ls
```

`dockerd --version` reports the local daemon binary. `docker version` reports the selected connection's client/server versions, which can refer to another machine. Confirm which environment you are studying.

On the disposable rootful Ubuntu VM only, inspect the managed service:

```bash
systemctl cat docker.service
systemctl show -p ExecStart docker.service
```

Read the effective startup options and all displayed drop-ins. A distribution may supply `-H fd://`; preserve it. The service exercise below will not add a `hosts` key.

### 2. Write a candidate JSON file

This creates only private temporary files in the local filesystem. It does not change `/etc/docker/daemon.json`.

```bash
daemon_lab_dir="$(mktemp -d -t docker-daemon-flags.XXXXXX)"
cat > "$daemon_lab_dir/daemon.json" <<'JSON'
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3"
  }
}
JSON
```

Here `max-size` requests rotation around the selected log-file size, and `max-file` limits retained files when rotation is configured. The strings `"10m"` and `"3"` belong to the logging driver. Validation does not change an existing container's logs.

### 3. Validate file-only and mixed-source settings

```bash
dockerd --validate --config-file "$daemon_lab_dir/daemon.json"
dockerd --validate --config-file "$daemon_lab_dir/daemon.json" --debug
```

Both are expected to validate: the file omits `debug`, so the second command supplies a distinct option. `--validate` exits rather than launching a daemon; the second command therefore does not enable debugging in a running service.

For the equivalent logging options supplied entirely as flags, select an empty configuration file so default system-file settings cannot accidentally overlap:

```bash
printf '{}\n' > "$daemon_lab_dir/empty.json"
dockerd --validate --config-file "$daemon_lab_dir/empty.json" \
  --log-driver=json-file \
  --log-opt=max-size=10m \
  --log-opt=max-file=3
```

This explains how `--log-opt` can be repeated for different keys. It validates a candidate, not the configuration currently loaded by a service. A successful configuration check also does not prove that every runtime dependency, permission, directory, or certificate is suitable for startup.

### 4. Observe a duplicate-setting failure

The next validation is **intentionally invalid**. It should return a non-zero exit code without starting a daemon because `log-driver` is present in both places.

```bash
if dockerd --validate --config-file "$daemon_lab_dir/daemon.json" \
  --log-driver=json-file; then
  printf 'Unexpected success: review this Engine version and configuration.\n'
else
  printf 'Expected validation failure: log-driver is duplicated.\n'
fi
```

Read the actual error message. Syntax errors and unknown keys can also fail validation, so a non-zero result alone does not identify the cause. Remove the duplicate flag, or move that setting out of the file, to fix this example.

### 5. Optional VM exercise: back up and merge existing configuration

Continue **only inside the disposable rootful Ubuntu VM** described in the prerequisites. The following steps write `/etc/docker/daemon.json`, restart Docker, and later create a verification container. They can interrupt VM workloads. Keep console access and the original backup until rollback succeeds.

Use an unmanaged regular `daemon.json` file or an absent file. If configuration is a symlink or maintained by another configuration tool, use a fresh VM for this exercise. Confirm that the service reads `/etc/docker/daemon.json`: an explicit `--config-file` pointing elsewhere would make this exercise edit the wrong file. Confirm also that the effective `ExecStart` does not already define `--log-driver` or `--log-opt`; otherwise choose a fresh VM or resolve those sources deliberately before continuing.

These commands create a root-owned backup directory and copy the original JSON into the private lab directory for merging. A small `original-state` file records whether the original configuration was present or absent. Treat existing configuration as sensitive: it may contain proxy credentials. Do not publish it.

```bash
python3 --version
daemon_backup_dir="$(sudo mktemp -d /var/tmp/docker-daemon-flags-backup.XXXXXX)"
(
  set -eu
  : "${daemon_lab_dir:?Complete the temporary-file steps first}"
  : "${daemon_backup_dir:?Backup directory creation must succeed}"
  if sudo test -L /etc/docker/daemon.json; then
    printf 'Use a fresh VM: the configuration is a symlink.\n' >&2
    exit 1
  fi
  if sudo test -f /etc/docker/daemon.json; then
    sudo cp -a /etc/docker/daemon.json "$daemon_backup_dir/daemon.json"
    sudo cat "$daemon_backup_dir/daemon.json" > "$daemon_lab_dir/original.json"
    printf 'present\n' | sudo tee "$daemon_backup_dir/original-state" >/dev/null
  else
    printf '{}\n' > "$daemon_lab_dir/original.json"
    printf 'absent\n' | sudo tee "$daemon_backup_dir/original-state" >/dev/null
  fi
  chmod 600 "$daemon_lab_dir/original.json"
  printf 'Backup directory: %s\n' "$daemon_backup_dir"
)
```

Save the printed backup path. The parentheses run the backup block in a subshell; `set -eu` stops that block on an error or unset variable without changing your interactive shell's settings. Continue only after it succeeds. The private directory made by `mktemp` restricts access to its contents. Next, create **`candidate.json`** by preserving the original keys and updating only the JSON logging settings:

```bash
python3 - "$daemon_lab_dir/original.json" "$daemon_lab_dir/candidate.json" <<'PY'
import json
import sys
from pathlib import Path

original = Path(sys.argv[1])
candidate = Path(sys.argv[2])
config = json.loads(original.read_text())
if config.get("log-driver", "json-file") != "json-file":
    raise SystemExit("Use a fresh VM: this exercise assumes the json-file driver.")
config["log-driver"] = "json-file"
config.setdefault("log-opts", {}).update({"max-size": "10m", "max-file": "3"})
candidate.write_text(json.dumps(config, indent=2) + "\n")
candidate.chmod(0o600)
PY
dockerd --validate --config-file "$daemon_lab_dir/candidate.json"
```

The deliberate driver check avoids carrying options for a different driver into `json-file`. Review the candidate privately and keep unrelated settings. File validation alone does not include every flag in systemd's `ExecStart`; that is why the earlier unit inspection matters.

### 6. Optional VM exercise: apply and verify

This overwrites the VM's daemon configuration with the merged candidate and restarts its rootful service. A restart is required here to apply the new defaults. A systemd `daemon-reload` is needed when unit definitions change; this exercise changes only the JSON file.

```bash
sudo mkdir -p /etc/docker
sudo install -m 0600 "$daemon_lab_dir/candidate.json" /etc/docker/daemon.json
sudo systemctl restart docker.service
systemctl is-active docker.service
sudo docker --host unix:///var/run/docker.sock info --format '{{.LoggingDriver}}'
```

Expected results are an active service and `json-file` as its default container logging driver. The explicit client `--host` targets the VM's rootful socket, rather than relying on a saved context for these verification commands.

Create a **new** test container to verify inherited logging options. Use `daemon-flags-check` only if it is unused. The first command may pull Alpine and leaves a stopped container for inspection; it does not publish any ports.

```bash
sudo docker --host unix:///var/run/docker.sock run \
  --name daemon-flags-check alpine:3.22 \
  sh -c 'printf "daemon logging defaults applied\n"'
sudo docker --host unix:///var/run/docker.sock inspect \
  --format '{{json .HostConfig.LogConfig}}' daemon-flags-check
```

Successful inspection should identify `json-file` with `max-size` and `max-file` set to the candidate values. This checks saved options on that newly created container. It does not exercise actual log rotation under load or prove production readiness. An older container retains its previous logging configuration.

If restart fails, inspect the daemon journal and restore the backup using step 7. Journal output may contain operational details; review before sharing it.

```bash
sudo journalctl -u docker.service --since '5 minutes ago' --no-pager
```

### 7. Optional VM exercise: roll back

First preserve any diagnostics you need. This restores the original file, or deletes **only this lab-installed** `/etc/docker/daemon.json` if the original file was absent. It then restarts the daemon again, which can interrupt workloads. Use the backup path from step 5 and do not run this against a different machine or a later unrelated configuration.

```bash
(
  set -eu
  : "${daemon_backup_dir:?Recover the saved backup directory path first}"
  daemon_original_state="$(sudo cat "$daemon_backup_dir/original-state")"
  case "$daemon_original_state" in
    present)
      sudo cp -a "$daemon_backup_dir/daemon.json" /etc/docker/daemon.json
      ;;
    absent)
      sudo rm -- /etc/docker/daemon.json
      ;;
    *)
      printf 'Unknown backup state: inspect the backup before rollback.\n' >&2
      exit 1
      ;;
  esac
  sudo systemctl restart docker.service
  systemctl is-active docker.service
  sudo docker --host unix:///var/run/docker.sock info --format '{{.LoggingDriver}}'
)
```

Compare the resulting service configuration with the original backup. Previously created containers retain their own saved logging options after rollback; daemon defaults apply to subsequent container creation.

## Expected result

For file-only steps, the valid configurations should report success and the duplicate-setting example should fail. No additional daemon should start, and no service configuration should change.

Illustrative successful validation output, **not captured execution evidence**:

```text
configuration OK
```

For the optional VM exercise, the service should return to active state, the new verification container should inherit the logging settings, and rollback should restore the original file or its original absence. These results must be checked in your VM; reading this lesson does not establish them.

## Practice

In the same private lab directory, create **`practice.json`**:

```bash
cat > "$daemon_lab_dir/practice.json" <<'JSON'
{
  "debug": true,
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "5m",
    "max-file": "2"
  }
}
JSON
dockerd --validate --config-file "$daemon_lab_dir/practice.json"
```

**Success condition:** the file validates. Predict what adding `--debug` to the validation command will do, then try it as an intentional failure. Remove the duplicate flag and confirm validation succeeds again. Explain why neither validation changes the running daemon's logging configuration.

## Troubleshooting

| Symptom | Likely cause | Fix |
| --- | --- | --- |
| `dockerd` not found, but `docker` works | Client-only installation or Docker Desktop managing the engine elsewhere | Use the Engine VM prerequisite or Desktop's configuration interface; installing a second daemon is not part of this exercise. |
| Invalid JSON | Comments, a trailing comma, or incorrect quoting | Correct the candidate and rerun validation. Quote logging option values as strings. |
| Unknown configuration key | Typo or an option unsupported by this Engine | Compare installed `dockerd --help` with its version's reference. |
| Option specified as a flag and in the file | Duplicate configuration sources | Inspect the candidate, service unit, and drop-ins; keep each setting in one source. |
| Daemon cannot start after adding `hosts` | Unit already provides `-H`, often `fd://` | Resolve the listener configuration through one source; restore the original file to recover. |
| PID/socket/address already in use | An accidental second `dockerd` startup | Stop attempting manual startup; inspect the managed service and use `--validate` for the file exercise. |
| Validation succeeds, but startup fails | Runtime path, permissions, service flags, or dependency issue | Inspect the daemon journal, preserve the original file, and roll back. Validation is not a live startup test. |
| Existing containers ignore new logging defaults | Their logging settings were saved at creation | Verify a new lab container. Recreating a real workload needs its own data and availability plan. |
| `systemctl` reports no Docker service | Wrong platform, Desktop, rootless setup, or different service manager | Use the platform table and the installation's actual service mechanism. |
| Changing `data-root` makes old resources appear missing | A different state directory/backend is being used | Restore the original configuration and plan storage migration separately. Do not delete the old directories. |

## Knowledge check

1. Does `docker --debug` enable daemon debug logging?
2. If a flag and JSON set `debug` to the same value, is the duplication accepted?
3. Does `dockerd --validate` apply the candidate to a running service?
4. Does changing daemon logging defaults update all existing containers?
5. Does changing `data-root` necessarily move containerd's image store?

> [!question]- Answers
> 1. No. It affects client diagnostics; daemon debugging is a service setting.
> 2. No. Duplicate settings across those sources fail even if their values match.
> 3. No. It validates the candidate and exits without starting or reconfiguring a daemon.
> 4. No. Verify defaults on newly created containers.
> 5. No. Inspect the backend and containerd storage configuration before any migration.

## Cleanup

If you completed the optional VM exercise, **finish rollback and verify the service first**. Do not delete the backup while recovery is unresolved. If you created the test container, this deletes its writable layer and associated local diagnostic history; the example process has already exited:

```bash
sudo docker --host unix:///var/run/docker.sock rm daemon-flags-check
```

After successful rollback, these commands delete only the known backup file and its now-empty directory. Skip this block if you did not create a backup directory.

```bash
if [ -n "${daemon_backup_dir:-}" ]; then
  if sudo test -f "$daemon_backup_dir/daemon.json"; then
    sudo rm -- "$daemon_backup_dir/daemon.json"
  fi
  sudo rm -- "$daemon_backup_dir/original-state"
  sudo rmdir -- "$daemon_backup_dir"
fi
```

For both file-only and VM exercises, remove the known temporary files when finished. They may contain a copy of the original configuration, so keep the directory private until removal. `rmdir` refuses to delete a directory containing additional files.

```bash
if [ -n "${daemon_lab_dir:-}" ] && [ -d "$daemon_lab_dir" ]; then
  for daemon_file in daemon.json empty.json original.json candidate.json practice.json; do
    if [ -f "$daemon_lab_dir/$daemon_file" ]; then
      rm -- "$daemon_lab_dir/$daemon_file"
    fi
  done
  rmdir -- "$daemon_lab_dir"
fi
```

The lab does not change Docker's state directories, provision remote listeners, or remove images or volumes. Keep the downloaded Alpine image if other lessons use it; no global pruning is required.

## Related lessons

- [[Docker/Docker Index|Docker learning path]]
- [[Docker/Docker Questions|Docker learner questions]]
- [[Docker/02 - Fundamentals/Docker CLI Flags and Combinations|Docker CLI Flags and Combinations]]
- [[01 - Docker Fundamentals/07 - Docker Commands|Docker Commands]]
- [[01 - Docker Fundamentals/05 - Docker Volumes|Docker Volumes]]
- [[01 - Docker Fundamentals/06 - Docker Networks|Docker Networks]]
- [[07 - References/01 - Docker Command Cheat Sheet|Docker Command Cheat Sheet]]
- [[08 - Dockerfile and Compose Mastery/27 - Security and Secrets|Security and Secrets]]
- [[08 - Dockerfile and Compose Mastery/30 - Final Assessment|Final Assessment]]

## References

- [Daemon command reference and configuration validation](https://docs.docker.com/reference/cli/dockerd/)
- [Daemon configuration, default file locations, and storage caveats](https://docs.docker.com/engine/daemon/)
- [Starting the daemon manually or through a service manager](https://docs.docker.com/engine/daemon/start/)
- [Live restore and its availability limitations](https://docs.docker.com/engine/daemon/live-restore/)
- [Remote access and systemd listener conflicts](https://docs.docker.com/engine/daemon/remote-access/)
- [Protecting the daemon socket with SSH or TLS](https://docs.docker.com/engine/security/protect-access/)
- [JSON file logging driver and rotation settings](https://docs.docker.com/engine/logging/drivers/json-file/)
- [Rootless configuration and service management](https://docs.docker.com/engine/security/rootless/tips/)
- [Docker Desktop Engine settings](https://docs.docker.com/desktop/settings-and-maintenance/settings/#docker-engine)
- [Client command reference](https://docs.docker.com/reference/cli/docker/)

---

## 📚 Lesson Navigation

| ⬅️ Previous Lesson | Next Lesson ➡️ |
|:------------------|----------------:|
| [[08 - Dockerfile and Compose Mastery/30 - Final Assessment\|Final Assessment]] | [[00 - Learning Roadmap\|Learning Roadmap]] |

🏠 [[00 - Learning Roadmap|Back to Learning Roadmap]]
