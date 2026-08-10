# XC Server Setup — Draft

!!! danger "Draft — Still Requires Final Review & Testing"
    These installation instructions and their downloadable configuration files have not completed final review or platform testing. Treat them as evaluation material, not finalized production deployment guidance.

The XC Server runs IPTVBoss continuously without the desktop interface and exposes the browser-based [Server Console](../index.md). Two installation paths are currently being prepared:

| Path | Best suited for | Persistent data | Process manager |
| --- | --- | --- | --- |
| [Docker](docker.md) | Hosts that already run Docker Compose | Docker named volume | Docker Compose |
| [Linux or macOS service](native-service.md) | A dedicated host without a container requirement | Host data directory | systemd or launchd |

Both paths put [Caddy](https://caddyserver.com/) in front of IPTVBoss. Caddy accepts public HTTPS connections while IPTVBoss listens only on the host's loopback interface. Do not expose IPTVBoss port `8001` directly to the Internet.

## Alpha release notice

!!! warning "Alpha software"
    The current examples use the public `walrusone/iptvboss-alpha` release channel and the `ghcr.io/walrusone/iptvboss-alpha:alpha` image. Alpha builds can change without notice. The examples will move to Beta and later to stable after those channels are published and tested.

For a long-running installation, pin an exact version after it has been tested instead of automatically following the moving `alpha` tag.

## Shared requirements

Before using either path, prepare:

- A server you control and can back up.
- A public hostname such as `boss.domain.com` with an `A` and, when applicable, `AAAA` record pointing to the server.
- Inbound TCP ports `80` and `443` forwarded to the server for Caddy's automatic HTTPS setup.
- Administrator or `sudo` access on the host.
- A plan for backing up the IPTVBoss data before upgrades or restores.

The basic Caddy configuration is deliberately small:

```caddyfile
boss.domain.com {
    reverse_proxy 127.0.0.1:8001
}
```

Caddy's [reverse-proxy defaults](https://caddyserver.com/docs/caddyfile/directives/reverse_proxy#defaults) preserve the original host and supply `X-Forwarded-For`, `X-Forwarded-Host`, and `X-Forwarded-Proto`. Do not add header overrides unless another trusted proxy or CDN creates a specific need for them.

## After installation

1. Confirm that the local health endpoint responds.
2. Open `https://boss.domain.com/boss.php`.
3. [Create the first administrator account and section PIN](../console/login.md).
4. If the server has no database, use [Link Restore](../console/link-restore.md) to complete bootstrap.
5. Review [Console Security](../console/security.md), then back up the working server data.

See [Windows hosting options](windows.md) before attempting either path on Windows.
