# Advanced Docker Configuration

Start with the [beginner Docker installation](docker.md). This page explains variations that are useful after the basic Compose setup is understood.

## How the Compose setup works

The downloaded Compose file runs one `iptvboss` service and stores its persistent data in one named volume:

| Volume | Contents |
| --- | --- |
| `iptvboss-data` | IPTVBoss databases, configuration, generated XC files, caches, and logs |

When [Caddy is added to the same Compose file](docker.md#bundle-caddy-in-the-same-compose-file), its `caddy-data` and `caddy-config` volumes store certificates and runtime configuration separately from IPTVBoss data.

The exact Docker volume names include the Compose project name, which normally comes from the installation directory. Run `docker compose config --volumes` from that directory to see the logical names, or `docker volume ls` to see Docker's full names.

## Image channel and version pinning

The image repository and tag are separate settings:

```env
IPTVBOSS_IMAGE=ghcr.io/walrusone/iptvboss-alpha
IPTVBOSS_TAG=alpha
```

This makes a future channel change an `.env` edit instead of a Compose-file edit. A moving channel tag receives new releases during `docker compose pull`. For a controlled production upgrade, set `IPTVBOSS_TAG` to an exact published version, create a backup, and then pull and recreate the service.

## Reverse proxies in another container or host

The standalone template publishes the backend on all host interfaces. When a reverse proxy runs directly on the Docker host, change it to host loopback:

```env
IPTVBOSS_HOST_IP=127.0.0.1
IPTVBOSS_HOST_PORT=8001
```

Bundled Caddy reaches `iptvboss:8001` over the Compose network, while a proxy process running directly on the host reaches `127.0.0.1:8001`. A proxy in another container or on another computer cannot reach the Docker host's loopback address.

There are two common advanced designs:

1. Attach the proxy and IPTVBoss to the same user-defined Docker network, then use `http://iptvboss:8001` as the upstream. A host-published port is unnecessary in this design.
2. Publish port `8001` on a host address the proxy can reach, then use that host address and port as the upstream.

For a shared network, first create it once:

```bash
sudo docker network create iptvboss-proxy
```

Create `compose.proxy.yaml` beside `compose.yaml`:

```yaml
services:
  iptvboss:
    networks:
      - iptvboss-proxy

networks:
  iptvboss-proxy:
    external: true
```

Set `IPTVBOSS_XC_BEHIND_HTTPS_PROXY=true` and `IPTVBOSS_HOST_IP=127.0.0.1`, attach the other proxy container to the same external network, and use `http://iptvboss:8001` as its upstream. Include the override file in every Compose command for this deployment:

```bash
sudo docker compose -f compose.yaml -f compose.proxy.yaml up --detach
```

The loopback-only host port may remain for health checks. Removing the `ports` entry is optional when the shared network is the only required access path.

For the second design, set a specific private address when possible:

```env
IPTVBOSS_HOST_IP=192.168.1.50
IPTVBOSS_HOST_PORT=8001
IPTVBOSS_XC_BEHIND_HTTPS_PROXY=true
IPTVBOSS_XC_BIND_ADDRESS=all
```

Use `0.0.0.0` only when the port must listen on every host interface. Protect it with the host firewall so only the proxy can connect. Never forward this HTTP port from the Internet.

Containers have separate loopback interfaces. `IPTVBOSS_XC_BIND_ADDRESS=loopback` prevents normal bridge-network access; keep `all` when another container must connect.

## Trusted proxy addresses

Proxy mode accepts forwarded client information. If untrusted systems can reach the published backend port, restrict which TCP peers may supply that information:

```env
IPTVBOSS_XC_TRUSTED_PROXIES=172.20.0.0/16,192.168.1.25/32
```

The value is a comma-separated list of proxy IP addresses or CIDR networks. Include every trusted proxy hop. Docker network address translation can make the peer appear as a bridge gateway instead of the address shown for the proxy container, so use the rejected-peer address reported in the IPTVBoss logs.

An invalid entry prevents XC Server startup. A request from outside the allowlist is rejected in proxy mode.

## Direct HTTPS

Direct HTTPS is intended for installations that cannot use an HTTPS reverse proxy. It makes IPTVBoss load a PKCS#12 private-key store:

```env
IPTVBOSS_XC_BEHIND_HTTPS_PROXY=false
IPTVBOSS_HTTPS_ONLY=true
IPTVBOSS_XC_KEYSTORE_PASSWORD=replace-with-the-keystore-password
IPTVBOSS_HOST_IP=0.0.0.0
```

Follow the complete [Direct HTTPS](direct-https.md#configure-docker) procedure. The keystore is needed only when IPTVBoss itself terminates HTTPS; reverse-proxy mode does not load it.

## Custom UID, GID, and bind mounts

The container runs without root privileges as UID/GID `10001:10001` by default:

```env
IPTVBOSS_UID=10001
IPTVBOSS_GID=10001
```

The named volume works with these defaults. Change them only when a bind-mounted host directory must use a different owner, as is common on some NAS platforms. The directory mapped to `/data` must already exist and be writable by the selected UID/GID.

Changing the IDs on an existing installation does not automatically change ownership of existing data. Stop IPTVBoss and correct the data ownership before restarting it.

## Restore a backup

Use a backup created with the beginner guide's `docker compose cp --archive` procedure. Stop IPTVBoss before copying its databases:

```bash
sudo docker compose stop iptvboss
sudo docker compose cp --archive ./backups/2026-08-23/. iptvboss:/data/
sudo docker compose start iptvboss
sudo docker compose logs --tail 200 iptvboss
```

Replace the example path with the backup being restored. Restoring overwrites files with matching names, so keep a copy of the current data until the restored server has been verified.

## Roll back an upgrade

A container rollback and a data rollback are separate operations. If a newer release changed the database schema, an older image may not safely open the newer data.

1. Stop IPTVBoss.
2. Restore the backup created immediately before the upgrade.
3. Set `IPTVBOSS_TAG` to the previous exact version.
4. Pull and recreate the service.
5. Check the health endpoint, logs, and Server Console.

```bash
sudo docker compose pull
sudo docker compose up --detach
sudo docker compose ps
```

## Docker Desktop and NAS platforms

The Compose files can be imported into Docker Desktop and many NAS container managers, but their networking and file-permission screens differ.

- Keep `/data` on persistent storage. Do not replace it with temporary container storage.
- Preserve the 60-second stop grace period so the database can close cleanly.
- Ensure `Caddyfile` is shared with the Docker Desktop virtual machine when using bundled Caddy.
- Map TCP `80`, TCP `443`, and optionally UDP `443` to Caddy, not to IPTVBoss.
- On NAS systems, match `IPTVBOSS_UID` and `IPTVBOSS_GID` to the owner of a bind-mounted app-data directory.
- A proxy supplied by the NAS or running in another container must follow the networking guidance above.

When a platform translates Compose into a graphical form, compare its generated configuration with `docker compose config` and confirm that the named volumes, environment variables, restart policy, and stop grace period remain present.

## Diagnose the resolved configuration

Compose reads `.env` before resolving `compose.yaml`. These commands show what it will actually run without starting anything:

```bash
sudo docker compose config
sudo docker compose config --services
sudo docker compose config --volumes
```

Do not post the full resolved configuration publicly when `.env` contains a direct-HTTPS keystore password.
