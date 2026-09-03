# Linux XC Server Installation

--8<-- "includes/xc-server-preview.md"

The Linux service runs IPTVBoss continuously without the desktop interface and exposes the browser-based [Server Console](../index.md). This guide uses the normal Linux package installation and a simple systemd service.

!!! note
    The service binds IPTVBoss to loopback. Put a reverse proxy such as [Caddy](https://caddyserver.com/) in front of it when the server must be reachable from other machines.

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

Change `User=ubuntu` to the normal Linux account that should run IPTVBoss. The account must be able to read and write the IPTVBoss data directory. No separate locked service account is required.

Enable and start the service:

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

Continue with [first-time setup](index.md#after-installation) after the local and public health checks succeed.
