# Server Basics

IPTVBoss can run as an XC Server without opening the desktop application. In this mode it runs continuously, provides the browser-based [Server Console](../server/index.md), and serves XC playlists, EPG data, and streams.

XC Server mode is normally run behind an HTTPS reverse proxy. IPTVBoss listens on the local machine, while the reverse proxy handles the public hostname, TLS certificate, and Internet-facing connection.

!!! warning "Protect remote connections"
    Direct HTTP is not encrypted. Do not expose a direct HTTP XC Server to the Internet. Use an HTTPS reverse proxy or configure direct HTTPS before allowing remote access.

## Recommended command

The recommended command for a host using Caddy or another HTTPS reverse proxy is:

```bash
iptvboss -xcserver -xc-proxy -xc-bind-address loopback
```

This command:

- starts XC Server mode;
- expects HTTPS to be terminated by a reverse proxy;
- binds IPTVBoss to `127.0.0.1`, so it is not directly reachable from the network.

The default XC Server port is `8001`. The platform setup pages explain how to install IPTVBoss, run this command under a service manager, and configure the reverse proxy:

- [Linux service](../server/setup/linux-service.md)
- [macOS service](../server/setup/macos-service.md)
- [Docker installation](../server/setup/docker.md)
- [Windows options](../server/setup/windows.md)

## XC Server flags

| Flag | Meaning |
| --- | --- |
| `-xcserver` | Start IPTVBoss as a headless XC Server. This is required for the server process. |
| `-xc-proxy` | Indicate that HTTPS is terminated by a reverse proxy. IPTVBoss expects requests to arrive with forwarded HTTPS information and does not use a local keystore. |
| `-httpsOnly` | Enable direct HTTPS instead of HTTP when no reverse proxy is being used. Direct HTTPS requires the PKCS#12 file `keystore.p12` in the IPTVBoss data directory and `IPTVBOSS_XC_KEYSTORE_PASSWORD`. Proxy mode takes precedence when both are enabled. |
| `-xc-bind-address loopback` | Listen only on `127.0.0.1`. This is the recommended value when using an HTTPS reverse proxy. |
| `-xc-bind-address all` | Listen on `0.0.0.0`, allowing connections through the host’s network interfaces. Use only when the firewall and transport security are configured appropriately. |
| `-directory PATH` | Store the IPTVBoss database, configuration, logs, keystore, and generated files under `PATH` instead of the operating-system default. The service account must be able to read and write this location. |
| `-xc-reset-admin` | Reset the XC administrator identity for the next server load. Stop the XC Server first; the command requires an interactive terminal and the exact confirmation `RESET XC ADMIN`. |

The bind address also accepts the equivalent literal addresses `127.0.0.1` and `0.0.0.0`.

## Data and port defaults

Unless `-directory` is supplied, IPTVBoss uses a per-user data directory:

| Platform | Default data directory |
| --- | --- |
| Linux and other Unix systems | `$HOME/IPTVBoss` |
| macOS | `~/Library/Application Support/IPTVBoss` |
| Windows | `%USERPROFILE%/IPTVBoss` |

The XC Server listens on port `8001` by default. The server port is configured in IPTVBoss settings and is not changed by the XC command-line flags described here.

## Listener selection

The effective listener is selected in this order:

1. `-xc-bind-address`, when supplied on the command line;
2. `IPTVBOSS_XC_BIND_ADDRESS`, when set;
3. the persisted **Block direct connections** server setting.

The environment variable and command-line option accept `loopback`, `all`, `127.0.0.1`, or `0.0.0.0`.

Example using the environment variable:

```bash
IPTVBOSS_XC_BIND_ADDRESS=loopback iptvboss -xcserver -xc-proxy
```

## HTTPS modes

### HTTPS reverse proxy

Use proxy mode when Caddy, Nginx, or another trusted reverse proxy provides HTTPS:

```bash
iptvboss -xcserver -xc-proxy -xc-bind-address loopback
```

Proxy mode can also be enabled with:

```bash
IPTVBOSS_XC_BEHIND_HTTPS_PROXY=true
```

The proxy must forward HTTPS requests and preserve the appropriate forwarded headers. `IPTVBOSS_XC_TRUSTED_PROXIES` can restrict which proxy addresses are allowed to provide forwarded client information; see [Proxy trust](#proxy-trust).

### Direct HTTP

Direct HTTP is the default when neither proxy mode nor HTTPS-only mode is enabled:

```bash
iptvboss -xcserver -xc-bind-address all
```

Use this only for an isolated local network or temporary bootstrap. Credentials, sessions, and other traffic are not encrypted.

### Direct HTTPS

Direct HTTPS requires a PKCS#12 keystore named `keystore.p12` in the selected IPTVBoss data directory and its password in the environment:

```bash
IPTVBOSS_XC_KEYSTORE_PASSWORD='change-this-password' \
  iptvboss -xcserver -httpsOnly -xc-bind-address all
```

The environment variable `IPTVBOSS_HTTPS_ONLY=true` enables the same mode. Do not put the keystore password directly in a shared service file when the service manager provides a safer secret mechanism.

The keystore contains the HTTPS private key and certificate chain. Its password does not protect XC users, administrator credentials, or the database. See [Direct HTTPS](../server/setup/direct-https.md) for certificate creation, deployment, verification, and renewal instructions.

## XC Server environment variables

| Variable | Meaning |
| --- | --- |
| `IPTVBOSS_XC_BIND_ADDRESS` | Select `loopback` or `all` when the command line does not specify `-xc-bind-address`. |
| `IPTVBOSS_XC_BEHIND_HTTPS_PROXY` | Set to `true` to enable HTTPS reverse-proxy mode without `-xc-proxy`. |
| `IPTVBOSS_HTTPS_ONLY` | Set to `true` to require direct HTTPS without `-httpsOnly`. |
| `IPTVBOSS_XC_KEYSTORE_PASSWORD` | Password that unlocks `keystore.p12` in direct HTTPS mode. It is unused in proxy and direct-HTTP modes. |
| `IPTVBOSS_XC_TRUSTED_PROXIES` | Optional comma-separated list of trusted proxy IP addresses or CIDRs. When set in proxy mode, forwarded client information is accepted only from those addresses. |

Boolean variables use `true` to enable the corresponding behavior. Proxy mode takes precedence over direct HTTPS mode, so a process configured for both expects HTTPS from the reverse proxy rather than loading the local keystore.

### Proxy trust

Proxy mode requires the request to arrive with forwarded HTTPS information. With no trusted-proxy list, proxy requests are accepted from the peer that connects to IPTVBoss. Set `IPTVBOSS_XC_TRUSTED_PROXIES` when you want an explicit allowlist:

```bash
IPTVBOSS_XC_TRUSTED_PROXIES=127.0.0.1/32,::1/128
```

Use the actual address or CIDR of the reverse proxy as seen by IPTVBoss. Do not list arbitrary public networks.

## Reset the XC administrator

Use the reset flag only when the administrator identity must be recovered:

```bash
iptvboss -xcserver -xc-reset-admin -directory /path/to/data
```

Stop every IPTVBoss process using that data directory first. The command refuses to run while the data is locked, asks for `RESET XC ADMIN`, and then clears the XC administrator identity, MFA, and trusted sessions. It preserves users, sources, layouts, settings, and backups. Start XC Server normally afterward; the console will ask you to create new administrator credentials.

For first-time initialization and normal administration, continue to the [Server Console](../server/index.md). For installation and service management, use the [XC Server setup overview](../server/setup/index.md).
