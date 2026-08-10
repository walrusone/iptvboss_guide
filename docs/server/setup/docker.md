# Docker XC Server Installation — Draft

!!! danger "Draft — Still Requires Final Review & Testing"
    This procedure and its templates still require hands-on Linux, macOS, and upgrade testing. It currently uses an Alpha image and is not finalized production guidance.

This path runs IPTVBoss in Docker while Caddy runs on the same host. Docker publishes XC port `8001` only on host loopback, so Caddy is the public entry point.

## Requirements

- Docker Engine or Docker Desktop with Docker Compose v2.
- Caddy installed on the host.
- The DNS and port requirements from the [setup overview](index.md).

## Download the templates

Create an empty working directory and download:

- [compose.yaml](../../assets/downloads/xc-server/compose.yaml)
- [iptvboss.env.example](../../assets/downloads/xc-server/iptvboss.env.example)
- [Caddyfile](../../assets/downloads/xc-server/Caddyfile)

Place the three files in that directory, then rename the environment example:

```bash
mv iptvboss.env.example .env
```

The Compose template currently uses:

```yaml
image: "ghcr.io/walrusone/iptvboss-alpha:${IPTVBOSS_TAG:-alpha}"
```

!!! warning "Moving Alpha tag"
    `IPTVBOSS_TAG=alpha` follows new Alpha publications. After validating a particular release, replace `alpha` in `.env` with its exact version to prevent an unplanned upgrade.

## Review the Docker settings

The supplied configuration intentionally sets:

| Setting | Value | Reason |
| --- | --- | --- |
| Host publication | `127.0.0.1:8001` | Prevents direct network access to XC |
| Container listener | `all` | Required across the container network boundary |
| HTTPS proxy mode | `true` | Requires Caddy's forwarded HTTPS signal |
| Direct HTTP | `false` | Rejects unprotected application traffic |
| Stop grace period | 60 seconds | Allows the JVM shutdown hook to close the databases |

The named volume holds databases, configuration, XC files, caches, and logs. The container runs as UID/GID `10001:10001` by default. Only change those IDs when replacing the named volume with a host bind mount whose ownership requires it.

Leave `IPTVBOSS_XC_TRUSTED_PROXIES` empty for this loopback-only draft. Docker NAT can make host traffic appear to originate from a bridge gateway. If strict proxy validation is enabled later, first identify the peer reported by IPTVBoss and allow only the verified address or CIDR.

## Start IPTVBoss

From the directory containing `compose.yaml` and `.env`:

```bash
docker compose config
docker compose pull
docker compose up --detach
docker compose logs --follow iptvboss
```

Press <kbd>Ctrl</kbd>+<kbd>C</kbd> to stop following logs; the container continues running.

In another terminal, check the local endpoint:

```bash
curl --fail http://127.0.0.1:8001/healthz
```

If it fails, inspect `docker compose logs iptvboss` before configuring public access.

## Configure Caddy

Replace `boss.domain.com` in the downloaded `Caddyfile` with the real public hostname.

=== "Linux"

    Install Caddy using the [official package instructions](https://caddyserver.com/docs/install). The Debian, Ubuntu, Raspbian, Fedora, RHEL, and CentOS packages include systemd service files.

    Validate and install the configuration:

    ```bash
    caddy validate --config ./Caddyfile
    sudo cp ./Caddyfile /etc/caddy/Caddyfile
    sudo systemctl reload caddy
    sudo systemctl status caddy
    ```

=== "macOS"

    Install Caddy with Homebrew:

    ```bash
    brew install caddy
    IPTVBOSS_CADDY_PREFIX=$(brew --prefix)
    caddy validate --config ./Caddyfile
    sudo cp ./Caddyfile "$IPTVBOSS_CADDY_PREFIX/etc/Caddyfile"
    sudo brew services start caddy
    sudo brew services list
    ```

    Running `brew services` with `sudo` registers a system LaunchDaemon that starts at boot. This behavior remains part of the draft test checklist.

Verify the public path:

```bash
curl --fail https://boss.domain.com/healthz
```

Then continue with [first-time setup](index.md#after-installation).

## Stop, back up, and upgrade

Stop without deleting data:

```bash
docker compose down
```

Back up the named volume using the host's established Docker-volume backup procedure before every upgrade. Verify that the backup is readable and record the IPTVBoss version that created it.

To upgrade after a backup:

1. Set `IPTVBOSS_TAG` in `.env` to the tested target version.
2. Pull and recreate the container:

    ```bash
    docker compose pull
    docker compose up --detach
    docker compose logs --follow iptvboss
    ```

3. Check both health endpoints and sign in to the console.

To roll back, stop the container, restore the matching pre-upgrade data backup if the database schema changed, set the previous version tag, and recreate the service.

!!! danger
    Do not run `docker compose down --volumes` unless the persistent IPTVBoss data is intentionally being deleted.
