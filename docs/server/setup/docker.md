# Docker XC Server Installation

!!! warning "Alpha release"
    This procedure uses the published IPTVBoss Alpha container image. Pin a tested version in `.env` for a long-running installation.

This path runs IPTVBoss in Docker while a reverse proxy such as Caddy runs on the host or elsewhere on the network. The container listens on port `8001` and stores its data in the named `iptvboss-data` volume.

## Requirements

- A supported Linux host or Docker Desktop installation.
- Docker Engine and Docker Compose v2. Follow Docker’s [official Engine installation instructions](https://docs.docker.com/engine/install/) for your operating system. Verify the installation with `docker version` and `docker compose version`.
- A reverse proxy for remote HTTPS access. The examples below use Caddy.
- The DNS, hostname, and port requirements from the [XC Server setup overview](index.md).

Do not expose direct HTTP XC Server traffic to the Internet. Use an HTTPS reverse proxy or configure direct HTTPS.

## Download the templates

Create a working directory and download these files:

- [compose.yaml](../../assets/downloads/xc-server/compose.yaml)
- [iptvboss.env.example](../../assets/downloads/xc-server/iptvboss.env.example)
- [Caddyfile](../../assets/downloads/xc-server/Caddyfile)

Place the files in the working directory and rename the environment example:

```bash
mv iptvboss.env.example .env
```

The Compose template uses `ghcr.io/walrusone/iptvboss-alpha:${IPTVBOSS_TAG:-alpha}`. `IPTVBOSS_TAG=alpha` follows new Alpha publications. After testing a release, replace it with the exact release version to prevent an unplanned upgrade.

## Configure the container

For a reverse proxy running directly on the Docker host, set these values in `.env`:

```env
IPTVBOSS_XC_BEHIND_HTTPS_PROXY=true
IPTVBOSS_HTTPS_ONLY=false
IPTVBOSS_XC_BIND_ADDRESS=all
IPTVBOSS_HOST_IP=127.0.0.1
IPTVBOSS_HOST_PORT=8001
```

| Setting | Meaning |
| --- | --- |
| `IPTVBOSS_XC_BEHIND_HTTPS_PROXY` | Requires HTTPS forwarded by the reverse proxy. |
| `IPTVBOSS_HTTPS_ONLY` | Enables direct HTTPS inside the container. Leave it `false` for proxy mode. |
| `IPTVBOSS_XC_BIND_ADDRESS` | Controls the listener inside the container. Use `all` for normal bridge networking; container loopback is not host loopback. |
| `IPTVBOSS_HOST_IP` | Controls which host interface publishes port `8001`. Use `127.0.0.1` for a host-local proxy. |
| `IPTVBOSS_HOST_PORT` | Changes the host-side port; the internal port remains `8001`. |
| `IPTVBOSS_UID` / `IPTVBOSS_GID` | Select the non-root container UID/GID, mainly for host bind mounts. |
| `IPTVBOSS_XC_TRUSTED_PROXIES` | Optional comma-separated proxy IP/CIDR allowlist for forwarded client information. |

The template runs as UID/GID `10001:10001`, drops all Linux capabilities, disables privilege escalation, and gives IPTVBoss 60 seconds to shut down cleanly.

Containers on the same bridge have separate loopback interfaces. If the reverse proxy is another container on a user-defined network, use `IPTVBOSS_XC_BIND_ADDRESS=all`, connect both containers to that network, and use `http://iptvboss:8001` as the upstream. Remove the `ports` mapping when host-published access is unnecessary.

## Start IPTVBoss

From the directory containing `compose.yaml` and `.env`:

```bash
docker compose config
docker compose pull
docker compose up --detach
docker compose logs --follow iptvboss
```

Press <kbd>Ctrl</kbd>+<kbd>C</kbd> to stop following logs; the container continues running. Verify the local health endpoint:

```bash
curl --fail http://127.0.0.1:8001/healthz
```

If the check fails, inspect `docker compose logs iptvboss` before configuring public access.

## Configure Caddy

Replace `boss.domain.com` in the downloaded [Caddyfile](../../assets/downloads/xc-server/Caddyfile) with the public hostname. Install Caddy using its [official installation instructions](https://caddyserver.com/docs/install), then validate and load the configuration:

```bash
caddy validate --config ./Caddyfile
sudo cp ./Caddyfile /etc/caddy/Caddyfile
sudo systemctl reload caddy
sudo systemctl status caddy
```

For macOS, install Caddy with Homebrew and register it as a service:

```bash
brew install caddy
IPTVBOSS_CADDY_PREFIX=$(brew --prefix)
caddy validate --config ./Caddyfile
sudo cp ./Caddyfile "$IPTVBOSS_CADDY_PREFIX/etc/Caddyfile"
sudo brew services start caddy
sudo brew services list
```

The proxy must send `X-Forwarded-Proto: https`. Verify the public path:

```bash
curl --fail https://boss.domain.com/healthz
```

Then open `https://boss.domain.com/boss.php`. On a new data volume, create the first administrator in the browser to complete bootstrap. Continue with [first-time setup](index.md#after-installation).

### Proxy trust

Docker traffic may appear to come from a bridge gateway rather than the proxy container’s displayed address. Leave `IPTVBOSS_XC_TRUSTED_PROXIES` empty for a host-local proxy. When the published port can be reached by untrusted peers, set an allowlist using the peer address reported in IPTVBoss logs:

```env
IPTVBOSS_XC_TRUSTED_PROXIES=172.17.0.6/32,127.0.0.1/32
```

An invalid allowlist prevents startup, and a request from a peer outside the allowlist is rejected. Include every trusted proxy network when another proxy sits in front of the local reverse proxy.

## Direct HTTPS alternative

For direct HTTPS without a reverse proxy, set:

```env
IPTVBOSS_XC_BEHIND_HTTPS_PROXY=false
IPTVBOSS_HTTPS_ONLY=true
IPTVBOSS_XC_BIND_ADDRESS=all
IPTVBOSS_XC_KEYSTORE_PASSWORD=replace-with-keystore-password
```

Mount the PKCS#12 file `keystore.p12` read-only at `/data/keystore.p12`. Follow the complete [Direct HTTPS](direct-https.md) procedure to create the certificate store, protect its password, configure the mount, and verify client trust. Proxy mode takes precedence over direct HTTPS. When both secure modes are disabled, direct HTTP is the default and is suitable only for isolated local-network use.

## Data, stop, and upgrade

All databases, configuration, XC files, caches, and logs are stored in the named `iptvboss-data` volume. Stop the container without deleting that data:

```bash
docker compose down
```

Back up the volume before every upgrade. To upgrade, set `IPTVBOSS_TAG` to the tested target version, then run:

```bash
docker compose pull
docker compose up --detach
docker compose logs --follow iptvboss
```

Check both health endpoints and sign in to the console. To roll back, restore the matching pre-upgrade data backup if the schema changed, set the previous version tag, and recreate the service.

!!! danger
    Do not run `docker compose down --volumes` unless the persistent IPTVBoss data is intentionally being deleted.
