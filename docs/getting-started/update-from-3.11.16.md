# Update from 3.11.16

Use this checklist when moving an existing IPTVBoss 3.11.16 installation to the stable release. If you run an XC Server, review the administrator login and connection mode before restarting it, then pair your desktop with the updated server.

## Back up and install

1. [Back up your existing installation](../settings/backups.md), including the XC Server data directory or Docker volume when applicable.
2. Record your server address, port, data directory, and service command or Docker configuration.
3. Stop IPTVBoss and its XC Server before replacing the application. Keep the same data directory, service account, or Docker volume so the updated application loads your existing configuration.
4. Install from the [official stable download page](https://walrusone.github.io/iptvboss-release/download.html) or [iptvboss-release releases](https://github.com/walrusone/iptvboss-release/releases/latest). Follow [Updating IPTVBoss](updating.md) and the [server backup and update procedures](../settings/backups.md#back-up-or-update-an-xc-server).
5. For an XC Server, apply the connection mode below, then start it and allow migration to finish. Do not run two processes against the same database.

!!! warning "Keep your existing data"
    Do not delete the data directory or Docker volume to complete an upgrade. If an existing server unexpectedly shows first-time setup or asks you to upload a database, check that it is using the original data location before proceeding.

## XC Server login now requires a username

The Server Console now uses an administrator **username and password**. When upgrading a server with an existing administrator password:

| Login field | What to enter after the update |
| --- | --- |
| Username | `admin` |
| Password | Your existing XC Server administrator password |

Open `/boss.php` at your server address and sign in with those credentials. The migration retains your password; `admin` is the default **username**, not a new password. These are Server Console credentials, separate from the XC usernames and layout passwords used by IPTV players.

After signing in, review [Console Security](../server/console/security.md) and configure the six-digit section PIN if prompted. The PIN protects sensitive console sections and is separate from the login password. A server without an existing administrator password uses [first-time administrator setup](../server/console/login.md#first-time-setup). If you cannot sign in, follow [XC Server Recovery](../server/recovery.md#reset-the-xc-administrator).

## Choose the correct server mode

Choose the mode based on how browsers, desktop installations, and players reach your server. The examples use the default port `8001`; keep your existing custom port where applicable.

| Your setup | Mode | Address clients use |
| --- | --- | --- |
| Caddy, Nginx, or another reverse proxy handles HTTPS | HTTPS reverse proxy | `https://boss.example.com` |
| Devices connect directly over a trusted local network | Direct HTTP | `http://192.168.1.50:8001` |
| IPTVBoss itself handles HTTPS with a certificate | Direct HTTPS | `https://boss.example.com:8001` |

### Reverse proxy handles HTTPS

For a proxy running directly on the same host as a native IPTVBoss installation, use:

```bash
iptvboss -xcserver -xc-proxy -xc-bind-address loopback
```

Keep any existing `-directory` and `-xc-port` arguments. Configure the proxy to forward to `http://127.0.0.1:8001` and supply `X-Forwarded-Proto: https`. IPTVBoss uses HTTP behind the proxy; the proxy handles the HTTPS certificate. You do not need `-httpsOnly` or a local `keystore.p12` for this setup.

Open the console through `https://boss.example.com/boss.php`. A direct HTTP visit to the backend is not a substitute for testing the proxy URL: proxy mode expects forwarded HTTPS information. Configure desktop and player connections with the proxy's HTTPS hostname. Do not append backend port `8001` to a public URL served on standard HTTPS port `443`.

For Docker, set `IPTVBOSS_XC_BEHIND_HTTPS_PROXY=true` and `IPTVBOSS_HTTPS_ONLY=false` in `.env`. Keep `IPTVBOSS_XC_BIND_ADDRESS=all` **inside the container** so the proxy can reach it. If the proxy runs directly on the Docker host, set `IPTVBOSS_HOST_IP=127.0.0.1` to keep the published backend port local to that host. A proxy in another container must use the shared Docker network; a proxy on another machine needs a reachable private backend address with access restricted to the proxy.

Follow [existing proxy setup](../server/setup/docker.md#use-an-existing-https-reverse-proxy), [bundled Caddy](../server/setup/docker.md#bundle-caddy-in-the-same-compose-file), or [proxies in another container or host](../server/setup/docker-advanced.md#reverse-proxies-in-another-container-or-host) for the full configuration. Review [proxy trust](../server/runtime.md#proxy-trust) if you use an explicit proxy allowlist.

### Direct connection on your local network

For devices that connect directly to the server's LAN address without a reverse proxy, use:

```bash
iptvboss -xcserver -xc-bind-address all
```

Keep any existing `-directory` and `-xc-port` arguments. Remove `-xc-proxy` and `-httpsOnly` from the launch command, and remove or set both `IPTVBOSS_XC_BEHIND_HTTPS_PROXY` and `IPTVBOSS_HTTPS_ONLY` to `false` in the service environment. A flag omitted from the command can still be enabled by an environment variable.

For Docker, use these `.env` settings:

```env
IPTVBOSS_XC_BEHIND_HTTPS_PROXY=false
IPTVBOSS_HTTPS_ONLY=false
IPTVBOSS_XC_BIND_ADDRESS=all
IPTVBOSS_HOST_IP=0.0.0.0
```

Open `http://192.168.1.50:8001/boss.php`, substituting your server's LAN address and port. Allow that port through the host firewall for your local network. Use the same HTTP address and port in desktop and player settings; enable **Include Port in URL** when needed. `localhost` and `127.0.0.1` refer to the device making the connection, so they will not reach your server from another device.

If you rely on the saved desktop setting instead of a bind-address override, clear **Block direct connections (bind to 127.0.0.1)** under **Settings → Server Settings** to allow LAN devices to connect. An explicit command-line bind address takes precedence over the environment variable and saved setting.

Direct HTTP does not encrypt passwords or traffic. Use it only on a trusted local network; do not forward this port from the Internet. Use an HTTPS proxy or direct HTTPS for public access.

### IPTVBoss handles HTTPS directly

If you do not use a reverse proxy and need IPTVBoss to serve HTTPS itself, remove proxy mode, enable `-httpsOnly` or `IPTVBOSS_HTTPS_ONLY=true`, and use `-xc-bind-address all` for connections from other devices. Provide `keystore.p12` in the server's data directory and its password through `IPTVBOSS_XC_KEYSTORE_PASSWORD`. Follow [Direct HTTPS](../server/setup/direct-https.md) for certificate setup and trust requirements.

Use `https://boss.example.com:8001/boss.php` with your hostname and port. Proxy mode takes precedence when both modes are enabled, so clear `-xc-proxy` and `IPTVBOSS_XC_BEHIND_HTTPS_PROXY` when switching to direct HTTPS.

## Restart and verify

For Docker installations previously using the alpha image, update the existing `.env` to the stable repository and tag:

```env
IPTVBOSS_IMAGE=ghcr.io/walrusone/iptvboss-release
IPTVBOSS_TAG=latest
```

If your Compose file hardcodes the image instead, change it to `ghcr.io/walrusone/iptvboss-release:latest`. Keep your existing volume mapping. You can use an exact published version tag instead of `latest` to control future upgrades.

1. Restart the native service after changing its launch settings. For Docker, run `docker compose config`, `docker compose pull`, and `docker compose up --detach` from the existing Compose directory after updating `.env`. A container restart alone does not apply changed environment settings. Preserve the existing volume and Compose project name.
2. Open the console at the address for your chosen mode and sign in as `admin` with your previous administrator password.
3. Confirm your layouts, users, sources, and schedules are present.
4. [Pair your desktop with the updated server](#pair-your-desktop-with-the-updated-server), or verify that its existing pairing still works.
5. Test a player connection, playlist, and EPG using an existing XC user's credentials.

If the console is unreachable, check the mode, listener address, port, firewall, and proxy headers before changing credentials or resetting the database. See [XC Server Troubleshooting](../server/troubleshooting.md) and [Runtime Configuration](../server/runtime.md).

## Pair your desktop with the updated server

Pairing connects each IPTVBoss desktop installation to the XC Server for database synchronization and automatic reload requests. Your existing administrator password is used to sign in to the console; it is not a pairing code. IPTV players continue to use their XC user credentials or output links and do not follow this desktop pairing procedure.

Complete the server update and confirm that the console shows the expected database before linking the updated desktop. If **Refresh Cloud Status** already confirms a working pairing, keep it; you do not need to unlink just because you updated.

!!! warning "The server database becomes the desktop database"
    When you link to an initialized XC Server, IPTVBoss restores the server's database over the database currently open on the desktop. It creates a verified local safety backup before restoring, but you should also back up both installations beforehand. If the desktop contains newer changes that are missing from the server, cancel pairing and resolve which database should be used before continuing. Pairing does not merge the two databases.

1. Open the updated server's `/boss.php` console and sign in with username `admin` and your existing administrator password, unless you have since changed those credentials.
2. Open **Paired Devices**, unlock the section with your PIN if prompted, and select **Generate Pairing Code**.
3. In the updated desktop application, open **Settings → Server Settings**.
4. Enter the server address reachable from that desktop. For a reverse proxy, use the public HTTPS hostname, such as `https://boss.example.com`. For direct LAN access, use the server's LAN address and listening port, such as `http://192.168.1.50:8001`. Review **Server Port**, **Include Port in URL**, and the displayed connection address where shown. Use the server's base address, not the `/boss.php` console page.
5. Enter a recognizable **Client Name**, paste the one-time **Pairing Code**, and select **Link XC Server**.
6. Review **Restore the initialized XC Server database?** and confirm only when the server holds the database you want this desktop to use. Wait for pairing and the database restore to finish.
7. Select **Refresh Cloud Status** if needed and confirm the desktop appears in the console's **Paired Devices** list. Review the desktop's **Server** dashboard for connection, synchronization, and backup status, then verify its layouts and sources.
8. Repeat with a fresh code for each additional desktop installation. Pairing codes expire and can be used only once.

Pairing configures the desktop to use XC Server database synchronization and provisions its automatic reload credential. Review your [database synchronization settings](../settings/backups.md) after linking, especially if you previously used Dropbox or Google Drive. See [automatic server reloads](../server/gui-settings.md#automatic-server-reloads-from-this-client) for when desktop changes reach the server.

If pairing succeeds but the database restore does not finish, use **Retry Current Database**. If an old or incorrect pairing prevents linking, back up any unpublished desktop changes before using **Unlink This Installation**, then generate a fresh code and repeat the steps above. Do not unlink a working pairing as a routine upgrade step.

If the server asks to initialize or upload a desktop database instead, stop and verify its original data directory or Docker volume. Uploading the desktop database is the [bootstrap workflow for an empty server](../server/gui-settings.md#bootstrap-an-empty-xc-server-with-gui-pairing), not the normal pairing flow for an upgraded server with existing data. See [Paired Devices](../server/console/paired-devices.md) and [XC Server Recovery](../server/recovery.md) for further help.
