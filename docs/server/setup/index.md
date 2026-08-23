# XC Server Setup

!!! danger "Draft paths"
    Docker and macOS service instructions still require final review and platform testing. The Linux service procedure is the tested native installation path.

The XC Server runs IPTVBoss continuously without the desktop interface and exposes the browser-based [Server Console](../index.md). Start with [Server Basics](../../getting-started/server.md) for the runtime model, available XC flags, defaults, and security modes, then choose the installation path for your host:

| Path | Best suited for | Persistent data | Process manager |
| --- | --- | --- | --- |
| [Docker](docker.md) | Ubuntu or Debian hosts; bundled Caddy is included | Docker named volumes | Docker Compose |
| [Linux service](linux-service.md) | A Linux host without a container requirement | IPTVBoss default or `-directory` | systemd |
| [macOS service](macos-service.md) | A macOS host without a container requirement | IPTVBoss default or `-directory` | launchd |

The Docker path includes [Caddy](https://caddyserver.com/) in the Compose stack by default. It can instead use an HTTPS reverse proxy already running on the same host. The native Linux and macOS paths install Caddy as a host service. In each case, Caddy accepts public HTTPS connections while the unencrypted IPTVBoss port is kept off the public network. Do not expose IPTVBoss port `8001` directly to the Internet.

For installations that cannot use a reverse proxy, see [Direct HTTPS](direct-https.md). IPTVBoss then terminates TLS itself using a PKCS#12 certificate store and normally listens at `https://host:8001`.

## Release channel

!!! warning "Pre-release software"
    The current examples use the public Alpha container channel. Pre-release builds can change without notice.

The Docker template keeps the image repository and version tag in `.env`, so changing to a future Beta or stable channel will not require editing `compose.yaml`. For a long-running installation, pin an exact tested version instead of automatically following a moving channel tag.

## Shared requirements

Before using either path, prepare:

- A server you control and can back up.
- A public hostname such as `boss.domain.com` with an `A` and, when applicable, `AAAA` record pointing to the server.
- Inbound TCP ports `80` and `443` forwarded to the server for Caddy's automatic HTTPS setup.
- Administrator or `sudo` access on the host.
- A plan for backing up the IPTVBoss data before upgrades or restores.

The basic Caddy configuration for native host installations is deliberately small:

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
4. If the server has no database, complete bootstrap in one of two ways:
    - Use [GUI pairing](../gui-settings.md#bootstrap-an-empty-xc-server-with-gui-pairing) to push the database currently open in an IPTVBoss Pro desktop installation.
    - Use [Link Restore](../console/link-restore.md) to initialize the server from a supported backup link.
5. Review [Console Security](../console/security.md), then back up the working server data.

See [Windows hosting options](windows.md) before attempting either path on Windows.
