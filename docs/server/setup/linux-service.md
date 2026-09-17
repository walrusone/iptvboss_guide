# Linux XC Server Installation

--8<-- "includes/xc-server-pro.md"

The Linux service runs IPTVBoss continuously without the desktop interface and exposes the browser-based [Server Console](../index.md). This guide uses the normal Linux package installation and a simple systemd service.

!!! note
    The service binds IPTVBoss to loopback. Put a reverse proxy such as [Caddy](https://caddyserver.com/) in front of it when the server must be reachable from other machines.

For a new public Ubuntu host, first follow [Prepare an Ubuntu VPS](prepare-vps.md).

## Install IPTVBoss

Install IPTVBoss normally using the [Linux installation instructions](../../getting-started/installation.md#linux). The package installs the `iptvboss` command at `/usr/bin/iptvboss`.

If you need the server data in a specific location, add the `-directory` option to `ExecStart`. For example, `-directory /srv/iptvboss` tells IPTVBoss where to keep its database and generated files. Create the directory and make sure the service user can write to it before starting the service.

The service command above uses the persisted XC Server port. After a GUI
configuration or another setup flow has saved the port, systemd reuses that
port after reboot. To make the port part of the service definition instead,
add an environment setting under `[Service]`:

```ini
Environment=IPTVBOSS_XC_PORT=9000
```

Alternatively, append `-xc-port 9000` to `ExecStart`. The command-line option
takes precedence over the environment variable. An external override applies
only to that process and does not change the persisted GUI setting.

## Configure the systemd service

Create `/etc/systemd/system/iptvboss.service`:

```ini
[Unit]
Description=IPTVBoss
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=10
User=ubuntu
ExecStart=/usr/bin/iptvboss -xcserver -xc-proxy -xc-bind-address loopback

[Install]
WantedBy=multi-user.target
```

Change `User=ubuntu` to the normal Linux account that should run IPTVBoss if your provider uses a different username. The account must be able to read and write the IPTVBoss data directory. This is the simplest setup for a beginner and keeps the default per-user data directory.

### Optional: use a dedicated service account

For stronger isolation, run IPTVBoss as a locked system account instead of your sudo-capable administrator account. Use this option for a new installation, or make a backup before migrating an existing data directory.

Create the account and its data directory:

```bash
sudo useradd --system --user-group \
    --home-dir /srv/iptvboss \
    --create-home \
    --shell /usr/sbin/nologin \
    iptvboss
sudo chown -R iptvboss:iptvboss /srv/iptvboss
sudo chmod 0750 /srv/iptvboss
```

Then use this service definition instead of the one above:

```ini
[Unit]
Description=IPTVBoss
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=10
User=iptvboss
ExecStart=/usr/bin/iptvboss -directory /srv/iptvboss -xcserver -xc-proxy -xc-bind-address loopback

[Install]
WantedBy=multi-user.target
```

The service account has no interactive shell and cannot use `sudo`. Keep `/srv/iptvboss` owned by `iptvboss:iptvboss` so the database, logs, configuration, and generated output remain writable by the service but are not stored in the administrator's home directory.

For an existing normal-user installation, replace `YOUR_ADMIN_USER` below with the account currently running IPTVBoss. Stop the service, copy the existing data including hidden files, and restore ownership before starting it again:

```bash
sudo systemctl stop iptvboss.service
sudo cp -a /home/YOUR_ADMIN_USER/IPTVBoss/. /srv/iptvboss/
sudo chown -R iptvboss:iptvboss /srv/iptvboss
```

If your existing installation uses a different data directory, copy that directory instead. Keep a backup before migrating.

## Enable and start the service

For either service-account choice, enable and start the service:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now iptvboss.service
sudo systemctl status iptvboss.service
```

Review the service log and confirm that the local health endpoint responds:

```bash
sudo journalctl -u iptvboss.service --no-pager -n 100
curl --fail http://127.0.0.1:8001/healthz
```

Replace `8001` in the health check with the configured XC port when an
override is in use.

## Put Caddy in front of IPTVBoss

Install Caddy using its [official distribution-specific instructions](https://caddyserver.com/docs/install). Create a Caddyfile such as:

```caddyfile
boss.domain.com {
    reverse_proxy 127.0.0.1:8001
}
```

Use the same configured XC port in the `reverse_proxy` upstream. The example
uses the default `8001`.

Replace `boss.domain.com` with the server’s public hostname, then validate and load the configuration:

```bash
caddy validate --config ./Caddyfile
sudo cp ./Caddyfile /etc/caddy/Caddyfile
sudo systemctl reload caddy
sudo systemctl status caddy
curl --fail https://boss.domain.com/healthz
```

Do not expose the XC listener port directly to the Internet.

If a reverse proxy cannot be used, follow [Direct HTTPS](direct-https.md) to install a PKCS#12 certificate store and change the service flags and environment securely.

## Back up and upgrade

Back up the IPTVBoss data directory before upgrading or restoring the installation. Stop the service before replacing the package or restoring files:

```bash
sudo systemctl stop iptvboss.service
sudo systemctl start iptvboss.service
```

Continue with [Server Console first-time setup](../console/login.md) after the local and public health checks succeed.
