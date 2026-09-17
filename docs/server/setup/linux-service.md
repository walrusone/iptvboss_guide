# Linux XC Server Installation

--8<-- "includes/xc-server-pro.md"

This walkthrough installs IPTVBoss as an always-on service on **Ubuntu Server 24.04 LTS**, then puts Caddy in front of it to provide a public HTTPS address. IPTVBoss runs without the desktop interface. You manage it through the browser-based [Server Console](../index.md) and a paired desktop installation.

For a new public server, complete [Prepare an Ubuntu VPS](prepare-vps.md) first. That page covers your administrator account, SSH, firewall, and hostname. Other Linux distributions need their own package and service instructions; the commands below assume Ubuntu.

!!! note "Already running a server?"
    For an existing 3.11.16 installation, start with [Update from 3.11.16](../../getting-started/update-from-3.11.16.md). Preserve the current service account and data directory. Do not replace a working service definition with the new-installation example below without checking its existing settings.

## Before you begin

You need:

- an Ubuntu server and a normal, non-root account with `sudo` access;
- an SSH connection to that server;
- a hostname such as `boss.example.com` pointing to the server;
- inbound TCP ports `80` and `443` allowed through the host and provider firewalls; and
- IPTVBoss Pro for XC Server.

Commands on this page run **on the Ubuntu server**, in your SSH window, unless a step explicitly says to use your computer's browser. If you need help connecting, follow [Connect to the VPS](prepare-vps.md#4-connect-to-the-vps-for-the-first-time).

Replace these placeholders before running commands or saving files:

| Placeholder | Replace it with |
| --- | --- |
| `SERVER_USER` | The normal Linux account that will run IPTVBoss, such as `ubuntu` or `bossadmin`. |
| `/home/SERVER_USER/IPTVBoss` | That account's IPTVBoss data directory. Use the actual home path if it differs. |
| `boss.example.com` | Your public IPTVBoss hostname. |
| `8001` | The XC Server port. Keep the same port in the service, Caddyfile, and health checks. |

This setup uses **HTTPS reverse-proxy mode**. IPTVBoss listens only at `127.0.0.1:8001`; Caddy accepts public connections on HTTPS port `443`. Do not open port `8001` to the Internet.

## 1. Confirm your account and prepare the tools

Run **on the server**:

```bash
whoami
printf '%s\n' "$HOME"
```

Record the username and home directory. If `whoami` shows `root`, return to [Create an administrator account](prepare-vps.md#7-create-an-administrator-account-if-needed) and reconnect as that account before continuing.

Install the tools used below:

```bash
sudo apt update
sudo apt install -y curl wget ca-certificates nano gnupg
```

`sudo` may ask for your Linux account password. Nothing appears while you type it; enter the password and press **Enter**.

<span id="install-iptvboss"></span>
## 2. Download and install IPTVBoss

1. In a browser **on your computer**, open the [official download page](https://walrusone.github.io/iptvboss-release/download.html).
2. Select **Linux** if the page shows another operating system.
3. Copy the command block under **Or install it from the command line**.
4. Paste it into your SSH terminal **on the Ubuntu server** and run it. Enter your Linux account password and approve installation if prompted.

The command detects the server's CPU architecture, downloads the matching `.deb`, and installs it with `apt`. Use the command from the download page so the filename matches the current release; there is no need to select an architecture or edit a download URL yourself.

Confirm the installation:

```bash
command -v iptvboss
```

The result should be `/usr/bin/iptvboss`. If the download or installation fails, resolve the reported error before continuing.

You do not need to open the desktop application on this server. The service will start IPTVBoss in headless XC Server mode.

## 3. Prepare the data directory

For the normal-account setup, run **without `sudo`**:

```bash
mkdir -p "$HOME/IPTVBoss"
ls -ld "$HOME/IPTVBoss"
```

The listing should show your normal account as the owner. This directory holds the database, configuration, logs, and generated output. The service definition below names it explicitly so its location is clear.

For example, if step 1 showed username `bossadmin` and home `/home/bossadmin`, use:

```text
User=bossadmin
-directory /home/bossadmin/IPTVBoss
```

These are values for the service file, not commands to run. If you prefer an isolated service account, follow [Optional: use a dedicated service account](#optional-use-a-dedicated-service-account) before creating the service, then use its username and directory in step 4.

<span id="configure-the-systemd-service"></span>
## 4. Create the systemd service

Systemd starts IPTVBoss at boot and restarts it if the process exits. First check for an existing service:

```bash
sudo systemctl cat iptvboss.service
```

On a new installation, `No files found for iptvboss.service` is expected. If a service definition appears, review it and use the [upgrade instructions](../../getting-started/update-from-3.11.16.md) instead of overwriting it.

For a new installation, open the service file:

```bash
sudo nano /etc/systemd/system/iptvboss.service
```

Paste the following, replacing **both** occurrences of `SERVER_USER` with your actual account name. If its home directory is not `/home/SERVER_USER`, change the data path accordingly:

```ini
[Unit]
Description=IPTVBoss XC Server
After=network.target
StartLimitIntervalSec=0

[Service]
Type=simple
Restart=always
RestartSec=10
User=SERVER_USER
ExecStart=/usr/bin/iptvboss -directory /home/SERVER_USER/IPTVBoss -xcserver -xc-proxy -xc-bind-address loopback

[Install]
WantedBy=multi-user.target
```

Save with **Ctrl+O**, press **Enter** to confirm the filename, and exit with **Ctrl+X**.

The command enables proxy mode and limits the IPTVBoss listener to the server itself. Do not add `-httpsOnly`: Caddy will handle the HTTPS certificate.

### If you use a different XC port

The service uses the saved XC Server port, or `8001` when none has been saved. To override it for this service, reopen the file and add this under `[Service]`, using your chosen port:

```ini
Environment=IPTVBOSS_XC_PORT=9000
```

Alternatively, append `-xc-port 9000` to the `ExecStart` line. The command-line option takes precedence over the environment variable; neither changes the saved GUI setting. Replace `8001` in every health check and Caddy upstream below with that same port.

<span id="enable-and-start-the-service"></span>
## 5. Start IPTVBoss and check the local connection

Run **on the server**:

```bash
sudo systemctl daemon-reload
sudo systemctl enable --now iptvboss.service
sudo systemctl status iptvboss.service --no-pager
```

`daemon-reload` makes systemd read the new file. `enable --now` starts IPTVBoss immediately and enables it at boot. The status should show `active (running)`.

Review startup messages:

```bash
sudo journalctl -u iptvboss.service --no-pager -n 100
```

Allow startup to finish, then check the local health endpoint:

```bash
curl --fail --show-error http://127.0.0.1:8001/healthz
```

The expected response is:

```json
{"status":"ok"}
```

This endpoint is available locally even in proxy mode. If it fails, check the service log, data-directory permissions, and configured port before proceeding.

Do not launch another IPTVBoss process to test it; the service already owns the database. The full console will be accessed through Caddy after HTTPS is configured.

<span id="put-caddy-in-front-of-iptvboss"></span>
## 6. Install Caddy

Caddy provides the public HTTPS listener and obtains the certificate for your hostname. If Caddy already runs on this host, keep the existing installation and continue to step 7. If another reverse proxy already owns ports `80` and `443`, configure that proxy for IPTVBoss instead of starting a competing Caddy service; see [HTTPS reverse-proxy mode](../runtime.md#https-reverse-proxy).

For a new installation, add the [official Caddy stable package repository](https://caddyserver.com/docs/install#debian-ubuntu-raspbian) **on the Ubuntu server**:

```bash
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl --fail --location --output /tmp/caddy-stable-key.asc https://dl.cloudsmith.io/public/caddy/stable/gpg.key
sudo gpg --dearmor --output /usr/share/keyrings/caddy-stable-archive-keyring.gpg /tmp/caddy-stable-key.asc
curl --fail --location --output /tmp/caddy-stable.list https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt
sudo install -m 644 /tmp/caddy-stable.list /etc/apt/sources.list.d/caddy-stable.list
sudo chmod 644 /usr/share/keyrings/caddy-stable-archive-keyring.gpg
sudo apt update
sudo apt install caddy
```

Run the commands in order and stop if any fails. If the key file already exists, check whether this repository was previously configured before replacing it. The package registers and starts the `caddy` systemd service.

Confirm the installation:

```bash
caddy version
sudo systemctl status caddy --no-pager
```

Expect a version number and `active (running)`. If Caddy cannot start because a port is already in use, identify the existing web server before continuing.

## 7. Open and edit the Caddyfile

The standard Ubuntu Caddy package reads **`/etc/caddy/Caddyfile`**. This is on the server, outside the IPTVBoss data directory. For a custom Caddy installation, run `sudo systemctl cat caddy` and use the path following `--config` in its launch command.

Back up the file and open it:

```bash
sudo cp --backup=numbered /etc/caddy/Caddyfile /etc/caddy/Caddyfile.pre-iptvboss
sudo nano /etc/caddy/Caddyfile
```

On a new installation, replace the default example site with:

```caddyfile
boss.example.com {
    reverse_proxy 127.0.0.1:8001
}
```

Replace `boss.example.com` with your real hostname. Replace `8001` if IPTVBoss uses another port. Caddy forwards to HTTP on the same machine and supplies the forwarded HTTPS headers.

If Caddy already serves other sites, preserve their blocks. Add one block for IPTVBoss, or edit its existing block rather than adding the same hostname twice.

Save with **Ctrl+O**, **Enter**, then exit with **Ctrl+X**.

## 8. Validate Caddy and load the configuration

Check the file before applying it:

```bash
sudo caddy validate --config /etc/caddy/Caddyfile --adapter caddyfile
```

Look for `Valid configuration`. If an error appears, reopen the file, correct the reported problem, and repeat the check. Do not reload a configuration that fails validation.

When validation succeeds, reload the [Caddy service](https://caddyserver.com/docs/running#using-the-service):

```bash
sudo systemctl reload caddy
sudo systemctl status caddy --no-pager
sudo journalctl -u caddy --no-pager -n 50
```

The service should remain `active (running)`. Allow a little time for the first certificate request. If issuance fails, confirm your hostname points to this server and ports `80` and `443` reach it. An `AAAA` record must point to working IPv6; remove or correct an invalid record in your DNS settings.

Check HTTPS **on the server**, using your real hostname:

```bash
curl --fail --show-error https://boss.example.com/healthz
```

Expect `{"status":"ok"}` without a certificate or HTTP error. Do not use `-k` to bypass a certificate problem; browsers, paired desktops, and players also need to trust the certificate.

## 9. Open the console and connect your desktop

In a browser **on your computer**, open:

```text
https://boss.example.com/boss.php
```

Use your real hostname. Do not append `:8001` to this public HTTPS address; that is the private backend port.

For a new server:

1. Follow [Login and Setup](../console/login.md#first-time-setup) to create an administrator username, password, and six-digit section PIN.
2. If the server has no database, follow [Bootstrap an empty XC Server with GUI pairing](../gui-settings.md#bootstrap-an-empty-xc-server-with-gui-pairing) to initialize it from the intended Pro desktop database, or use [Link Restore](../console/link-restore.md).
3. Wait for initialization to finish, then confirm the expected sources, layouts, and users are present.
4. Follow [First XC Server Connection](../first-connection.md) to connect additional desktops and test a player.

For an existing server upgraded from 3.11.16, sign in with username `admin` and your previous administrator password unless you have since changed them. Follow [the upgrade pairing walkthrough](../../getting-started/update-from-3.11.16.md#pair-your-desktop-with-the-updated-server). Linking to an initialized server restores its database onto the desktop; it does not merge the databases.

If an existing server unexpectedly appears empty, check the service's `User` and `-directory` before initializing it again.

## Optional: use a dedicated service account

For a **new installation**, you can run IPTVBoss as a locked account named `iptvboss` instead of your administrator account. Complete this section before step 4 if you want that arrangement.

Create the account and data directory **on the server**:

```bash
sudo useradd --system --user-group \
    --home-dir /srv/iptvboss \
    --create-home \
    --shell /usr/sbin/nologin \
    iptvboss
sudo chown iptvboss:iptvboss /srv/iptvboss
sudo chmod 0750 /srv/iptvboss
```

If the account or directory already exists, check what uses it before proceeding. In the step 4 service file, use these two lines in place of the normal-account values:

```ini
User=iptvboss
ExecStart=/usr/bin/iptvboss -directory /srv/iptvboss -xcserver -xc-proxy -xc-bind-address loopback
```

Continue from step 5. This account has no interactive shell; systemd runs IPTVBoss as that user. Its data directory is explicitly `/srv/iptvboss`, so use that path in backups too.

For an **existing installation**, do not switch `User` or `-directory` without migrating its data. First make a stopped-server backup using the next section. Keep the service stopped while copying the original data directory, including hidden files, into an empty destination and assigning ownership to the service account. Then change the service definition and confirm the original database loads. Keep the old directory and backup until verification succeeds.

## Back up and upgrade

### 1. Identify the data directory

Run **on the server**:

```bash
sudo systemctl cat iptvboss.service
```

Read the `-directory` argument. This walkthrough uses `/home/SERVER_USER/IPTVBoss`, or `/srv/iptvboss` with the dedicated account. Use your actual directory in the copy command below. For an older service without `-directory`, confirm the service user's [default data directory](../runtime.md#data-and-port-defaults).

### 2. Stop IPTVBoss and copy the data

Wait for active synchronization or backup operations to finish, then stop the service:

```bash
sudo systemctl stop iptvboss.service
sudo systemctl is-active iptvboss.service
```

The result should be `inactive`. If it is still stopping or another IPTVBoss process uses the database, wait and resolve that before copying files.

Create a dated backup directory outside the live data directory:

```bash
IPTVBOSS_BACKUP_DIR="$HOME/iptvboss-backups/$(date +%Y%m%d-%H%M%S)"
mkdir -p -m 700 "$IPTVBOSS_BACKUP_DIR"
sudo cp -a /home/SERVER_USER/IPTVBoss "$IPTVBOSS_BACKUP_DIR/data"
sudo cp -a /etc/caddy/Caddyfile "$IPTVBOSS_BACKUP_DIR/Caddyfile"
sudo systemctl cat iptvboss.service > "$IPTVBOSS_BACKUP_DIR/iptvboss-service.txt"
sudo ls -la "$IPTVBOSS_BACKUP_DIR/data"
```

Replace `/home/SERVER_USER/IPTVBoss` before running the copy command. For the dedicated account, use `/srv/iptvboss`. The service text records the effective configuration; also retain any custom environment files it references.

Confirm the copy contains your expected database and other data files, then retain another copy on separate storage. Keep backups private because they contain credentials and configuration. If a copy fails, resolve it before installing an update.

### 3. Install the update and restart

For a backup only, skip the installer and start the service again. For an update, keep it stopped while downloading and installing the new stable package using [step 2](#2-download-and-install-iptvboss). Keep the existing service account, data directory, and Caddy configuration. When updating from 3.11.16, apply the [migration instructions](../../getting-started/update-from-3.11.16.md) before starting it.

Then run:

```bash
sudo systemctl daemon-reload
sudo systemctl start iptvboss.service
sudo systemctl status iptvboss.service --no-pager
sudo journalctl -u iptvboss.service --no-pager -n 100
curl --fail --show-error https://boss.example.com/healthz
```

Use your real hostname in the last command. Wait for any migration to complete, sign in to the console, and verify the database, desktop pairing, and player access. Do not restore data files while the service is running; see [Restore a Database Backup](../../troubleshooting/restore.md) for recovery options.

## Common problems

| Symptom | What to check |
| --- | --- |
| `iptvboss: command not found` | Confirm the `.deb` installation finished and `command -v iptvboss` returns `/usr/bin/iptvboss`. |
| Service fails with `217/USER` | Check that `User` contains an existing account name, not the literal `SERVER_USER`. |
| Permission denied opening the database | Check that the service account owns or can write the directory named by `-directory`. |
| Local health check fails | Inspect `journalctl -u iptvboss.service`; confirm startup completed and the port matches the service configuration. |
| Caddy cannot bind ports `80` or `443` | Check whether another web server or Docker container already uses those ports. |
| Caddy reports a gateway error | Confirm IPTVBoss is running and the Caddyfile points to `127.0.0.1` and the correct XC port. |
| Certificate issuance fails | Check DNS, any IPv6 record, inbound ports `80` and `443`, and `journalctl -u caddy`. |
| The console looks like a new installation after an update | Check the service account and data directory before uploading or restoring a database. |

For private LAN HTTP or direct HTTPS instead of Caddy, use the appropriate [server-mode instructions](../../getting-started/update-from-3.11.16.md#choose-the-correct-server-mode) and [Direct HTTPS setup](direct-https.md). Do not simply expose this proxy-mode backend to the Internet.
