# Install the XC Server with Docker

This guide installs IPTVBoss and an HTTPS web server with Docker Compose. It is written for a beginner using Ubuntu or Debian. The same Compose files can also be used with an HTTPS reverse proxy that is already installed on the Docker host.

The recommended setup starts two containers:

- **IPTVBoss** runs the XC Server and stores its data in a Docker volume.
- **Caddy** provides the public HTTPS address and renews its certificate automatically.

Port `8001` remains available only on the Docker host. It is not exposed directly to the Internet.

## 1. Prepare the hostname and network

Choose a hostname for the server, such as `boss.example.com`.

Before starting the containers:

1. Create a DNS `A` record that points the hostname to your public IPv4 address. Add an `AAAA` record only if the server has working public IPv6.
2. Forward TCP ports `80` and `443` from the router to the Docker host.
3. Allow TCP ports `80` and `443` through the host firewall. Caddy can also use UDP port `443` for HTTP/3, but TCP `443` is still required.
4. Confirm that no other program is already using host ports `80` or `443`.

!!! note
    Automatic public HTTPS normally requires a publicly reachable hostname. If the Internet provider uses carrier-grade NAT, or if this is a private-LAN-only installation, an existing proxy, VPN, or private certificate setup may be needed. See [Advanced Docker configuration](docker-advanced.md).

## 2. Install Docker

Install Docker Engine by following Docker's official instructions for [Ubuntu](https://docs.docker.com/engine/install/ubuntu/) or [Debian](https://docs.docker.com/engine/install/debian/). Install the Docker Compose plugin when the instructions list the packages to install.

Verify both tools:

```bash
sudo docker version
sudo docker compose version
```

Both commands must finish without an error. This guide uses `sudo` because that works with Docker's default Linux permissions.

## 3. Download the setup files

Create a directory for the installation:

```bash
mkdir -p ~/iptvboss-xc
cd ~/iptvboss-xc
```

Download all three files into that directory:

- [compose.yaml](../../assets/downloads/xc-server/compose.yaml)
- [iptvboss.env.example](../../assets/downloads/xc-server/iptvboss.env.example)
- [Caddyfile](../../assets/downloads/xc-server/Caddyfile)

Rename the environment example to `.env`:

```bash
mv iptvboss.env.example .env
```

The directory should now contain `compose.yaml`, `.env`, and `Caddyfile`:

```bash
ls -la
```

## 4. Choose the HTTPS setup

### Recommended: use the bundled Caddy container

Open `.env` in a text editor:

```bash
nano .env
```

Replace `boss.domain.com` with the hostname prepared in step 1:

```env
COMPOSE_PROFILES=caddy
IPTVBOSS_DOMAIN=boss.example.com
```

Leave the other settings unchanged. Save with <kbd>Ctrl</kbd>+<kbd>O</kbd>, press <kbd>Enter</kbd>, and exit with <kbd>Ctrl</kbd>+<kbd>X</kbd>.

### Alternative: use an existing proxy on this host

Use this option only when Caddy, Nginx, Apache, or another HTTPS proxy already runs directly on the same Docker host.

Edit `.env` and clear the profile value:

```env
COMPOSE_PROFILES=
```

Leave these settings unchanged:

```env
IPTVBOSS_HOST_IP=127.0.0.1
IPTVBOSS_HOST_PORT=8001
IPTVBOSS_XC_BEHIND_HTTPS_PROXY=true
```

Configure the existing proxy to send HTTPS requests to this HTTP upstream:

```text
http://127.0.0.1:8001
```

The proxy must supply `X-Forwarded-Proto: https`. Most established reverse-proxy configurations do this automatically.

!!! note
    A reverse proxy in another container cannot use the Docker host's `127.0.0.1`. Follow [Reverse proxies in another container or host](docker-advanced.md#reverse-proxies-in-another-container-or-host) instead.

## 5. Check and start the containers

From `~/iptvboss-xc`, validate the configuration:

```bash
sudo docker compose config
```

Correct any reported error before continuing. Pull the images and start the installation:

```bash
sudo docker compose pull
sudo docker compose up --detach
```

Check its status:

```bash
sudo docker compose ps
```

With bundled Caddy, both `iptvboss` and `caddy` should be running. Caddy may take a short time to obtain the first certificate.

Follow the logs while the services start:

```bash
sudo docker compose logs --follow
```

Press <kbd>Ctrl</kbd>+<kbd>C</kbd> to stop viewing the logs. The containers continue running.

## 6. Open the Server Console

Check the public health address, replacing the example hostname:

```bash
curl --fail https://boss.example.com/healthz
```

Then open this address in a browser:

```text
https://boss.example.com/boss.php
```

On a new installation, the first visit creates the administrator account. Continue with [first-time setup](index.md#after-installation).

## Common commands

Run these commands from `~/iptvboss-xc`.

| Task | Command |
| --- | --- |
| Show status | `sudo docker compose ps` |
| View recent logs | `sudo docker compose logs --tail 200` |
| Follow logs | `sudo docker compose logs --follow` |
| Restart the services | `sudo docker compose restart` |
| Stop the services | `sudo docker compose down` |
| Start them again | `sudo docker compose up --detach` |

The data remains in the `iptvboss-data` Docker volume when the services are stopped or recreated.

!!! danger
    Never run `docker compose down --volumes` unless the IPTVBoss data is intentionally being deleted. The `--volumes` option removes persistent data.

## Back up the server

Back up the data before every upgrade and on a regular schedule. The following example creates a directory named for the backup date; replace the date with today's date:

```bash
mkdir -p backups/2026-08-23
sudo docker compose stop iptvboss
sudo docker compose cp --archive iptvboss:/data/. ./backups/2026-08-23/
sudo docker compose start iptvboss
```

Copy the resulting backup to another computer or storage device. A backup kept only on the Docker host does not protect against failure of that host.

## Update IPTVBoss

Create a backup first, then run:

```bash
sudo docker compose pull
sudo docker compose up --detach
sudo docker compose ps
```

Check the logs and sign in after the update. Version pinning, restore, and rollback procedures are in [Advanced Docker configuration](docker-advanced.md).

## Quick troubleshooting

| Problem | What to check |
| --- | --- |
| Caddy cannot obtain a certificate | Confirm the DNS record points to this host and inbound TCP ports `80` and `443` reach it. Check `sudo docker compose logs caddy`. |
| Host port `80` or `443` is already in use | Stop the existing web server or use it as the [existing proxy](#alternative-use-an-existing-proxy-on-this-host). |
| Only the `iptvboss` service starts | Confirm `.env` contains `COMPOSE_PROFILES=caddy`. If it does, check the IPTVBoss logs because Caddy waits for that service to become healthy. |
| The browser shows a gateway error | Check `sudo docker compose ps` and `sudo docker compose logs iptvboss`. Wait for IPTVBoss to become healthy. |
| The public URL works but `/boss.php` does not | Include `/boss.php` exactly and inspect the IPTVBoss logs for the request. |
| Docker reports permission denied | Use `sudo` with the Docker commands, or follow Docker's official post-installation instructions to configure non-root access. |

For Docker Desktop, NAS systems, direct HTTPS, nonstandard networks, or custom file ownership, continue to [Advanced Docker configuration](docker-advanced.md).
