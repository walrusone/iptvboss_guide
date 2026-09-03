# Install the XC Server with Docker

--8<-- "includes/xc-server-preview.md"

This guide runs IPTVBoss as a standalone Docker container and stores its data in a Docker volume. It is written for a beginner using Ubuntu or Debian.

The default setup publishes unencrypted HTTP on port `8001` so other devices on a trusted local network can reach the Server Console.

!!! danger
    Do not forward port `8001` from the Internet. For public access, [bundle Caddy in the same Compose file](#bundle-caddy-in-the-same-compose-file), use an existing HTTPS reverse proxy, or configure [direct HTTPS](direct-https.md).

## 1. Install Docker

Install Docker Engine by following Docker's official instructions for [Ubuntu](https://docs.docker.com/engine/install/ubuntu/) or [Debian](https://docs.docker.com/engine/install/debian/). Install the Docker Compose plugin when the instructions list the packages to install.

Verify both tools:

```bash
sudo docker version
sudo docker compose version
```

Both commands must finish without an error. This guide uses `sudo` because that works with Docker's default Linux permissions.

## 2. Download the setup files

Create a directory for the installation:

```bash
mkdir -p ~/iptvboss-xc
cd ~/iptvboss-xc
```

Download both files into that directory:

- [compose.yaml](../../assets/downloads/xc-server/compose.yaml)
- [iptvboss.env.example](../../assets/downloads/xc-server/iptvboss.env.example)

Rename the environment example to `.env`:

```bash
mv iptvboss.env.example .env
```

The directory should now contain `compose.yaml` and `.env`:

```bash
ls -la
```

## 3. Review the standalone settings

Open `.env` in a text editor:

```bash
nano .env
```

The downloaded environment file defaults to:

```env
IPTVBOSS_HOST_IP=0.0.0.0
IPTVBOSS_HOST_PORT=8001
IPTVBOSS_XC_PORT=8001
IPTVBOSS_XC_BEHIND_HTTPS_PROXY=false
IPTVBOSS_HTTPS_ONLY=false
```

`0.0.0.0` publishes port `8001` on every host network interface. Keep this only when the host firewall and router restrict access to a trusted network. To allow access through one host address instead, replace it with that private address.

`IPTVBOSS_XC_PORT` controls the port IPTVBoss listens on inside the container.
`IPTVBOSS_HOST_PORT` is the existing Docker host-side publication port. The
Compose template maps the host port to the configured XC listener port.

Leave the remaining settings unchanged. Save with <kbd>Ctrl</kbd>+<kbd>O</kbd>, press <kbd>Enter</kbd>, and exit with <kbd>Ctrl</kbd>+<kbd>X</kbd>.

## 4. Check and start IPTVBoss

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

The `iptvboss` service should be running.

Follow the logs while the services start:

```bash
sudo docker compose logs --follow
```

Press <kbd>Ctrl</kbd>+<kbd>C</kbd> to stop viewing the logs. The containers continue running.

## 5. Open the Server Console

From another device on the same network, replace the example address with the Docker host's private IP address:

```bash
curl --fail http://192.168.1.50:8001/healthz
```

Then open this address in a browser:

```text
http://192.168.1.50:8001/boss.php
```

On a new installation, the first visit creates the administrator account. Continue with [first-time setup](index.md#after-installation).

## Bundle Caddy in the same Compose file

Use this option to give IPTVBoss a public HTTPS address with automatically renewed certificates. Choose a hostname such as `boss.example.com`, then:

1. Create a DNS `A` record that points the hostname to the Docker host's public IPv4 address. Add an `AAAA` record only when the host has working public IPv6.
2. Forward TCP ports `80` and `443` from the router to the Docker host.
3. Allow TCP ports `80` and `443` through the host firewall. UDP port `443` is optional for HTTP/3.
4. Confirm that no other program is using host ports `80` or `443`.

Download [Caddyfile](../../assets/downloads/xc-server/Caddyfile) into `~/iptvboss-xc` beside `compose.yaml`.

Edit `.env` and change or add these values:

```env
IPTVBOSS_DOMAIN=boss.example.com
IPTVBOSS_HOST_IP=127.0.0.1
IPTVBOSS_XC_BEHIND_HTTPS_PROXY=true
```

Proxy mode requires requests to arrive through an HTTPS proxy. Binding the published port to `127.0.0.1` also prevents other computers from bypassing Caddy over unencrypted HTTP.

Add the `caddy` service under `services` in `compose.yaml`, aligned with the existing `iptvboss` service:

```yaml
  caddy:
    image: caddy:2-alpine
    restart: unless-stopped
    environment:
      IPTVBOSS_DOMAIN: "${IPTVBOSS_DOMAIN:-boss.domain.com}"
    ports:
      - "80:80"
      - "443:443"
      - "443:443/udp"
    volumes:
      - ./Caddyfile:/etc/caddy/Caddyfile:ro
      - caddy-data:/data
      - caddy-config:/config
    depends_on:
      iptvboss:
        condition: service_healthy
```

Add the two Caddy volumes to the existing `volumes` section at the end of the file:

```yaml
volumes:
  iptvboss-data:
  caddy-data:
  caddy-config:
```

Validate and apply the edited configuration:

```bash
sudo docker compose config
sudo docker compose pull
sudo docker compose up --detach
sudo docker compose ps
```

Both `iptvboss` and `caddy` should be running. Caddy may take a short time to obtain the first certificate. Follow its logs if necessary:

```bash
sudo docker compose logs --follow caddy
```

Verify the public address, then open the console:

```bash
curl --fail https://boss.example.com/healthz
```

```text
https://boss.example.com/boss.php
```

!!! note
    Automatic public HTTPS normally requires a publicly reachable hostname. Carrier-grade NAT and private-LAN-only installations may require an existing proxy, VPN, or private certificate setup.

## Use an existing HTTPS reverse proxy

When Caddy, Nginx, Apache, or another HTTPS proxy already runs directly on the Docker host, edit `.env`:

```env
IPTVBOSS_HOST_IP=127.0.0.1
IPTVBOSS_HOST_PORT=8001
IPTVBOSS_XC_PORT=8001
IPTVBOSS_XC_BEHIND_HTTPS_PROXY=true
```

Configure the proxy to send HTTPS requests to this HTTP upstream:

```text
http://127.0.0.1:8001
```

The proxy must supply `X-Forwarded-Proto: https`; established reverse-proxy configurations normally do this automatically. Validate and recreate IPTVBoss after changing `.env`:

```bash
sudo docker compose config
sudo docker compose up --detach
```

A proxy in another container or on another computer cannot use the Docker host's `127.0.0.1`. Follow [Reverse proxies in another container or host](docker-advanced.md#reverse-proxies-in-another-container-or-host) for those designs. When using a custom XC port, use that same port in the proxy upstream.

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
| Another LAN device cannot connect | Confirm the URL uses the Docker host's private address and port `8001`. Check the host firewall and `IPTVBOSS_HOST_IP`. |
| Caddy cannot obtain a certificate | Confirm the DNS record points to this host and inbound TCP ports `80` and `443` reach it. Check `sudo docker compose logs caddy`. |
| Host port `80` or `443` is already in use | Stop the existing web server or [use it as the proxy](#use-an-existing-https-reverse-proxy). |
| Only the `iptvboss` service starts after adding Caddy | Run `sudo docker compose config --services` and confirm the `caddy` block is under `services`. Check YAML indentation and the IPTVBoss health status. |
| The browser shows a gateway error | Check `sudo docker compose ps` and `sudo docker compose logs iptvboss`. Wait for IPTVBoss to become healthy. |
| The public URL works but `/boss.php` does not | Include `/boss.php` exactly and inspect the IPTVBoss logs for the request. |
| Docker reports permission denied | Use `sudo` with the Docker commands, or follow Docker's official post-installation instructions to configure non-root access. |

For Docker Desktop, NAS systems, direct HTTPS, nonstandard networks, or custom file ownership, continue to [Advanced Docker configuration](docker-advanced.md).
