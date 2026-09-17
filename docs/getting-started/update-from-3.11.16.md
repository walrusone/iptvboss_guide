# Update from 3.11.16

Use this checklist when moving an existing IPTVBoss 3.11.16 installation to the stable release.

1. [Back up and install](#back-up-and-install).
2. For XC Server, review the [administrator login change](#xc-server-login-now-requires-a-username), then expand [your server mode](#choose-the-correct-server-mode).
3. [Restart and verify](#restart-and-verify) using the instructions for your installation.
4. [Pair your desktop](#pair-your-desktop-with-the-updated-server), or confirm its existing pairing still works.

If you only use the desktop application and do not run or connect to an XC Server, follow [Updating IPTVBoss](updating.md). The server steps below do not apply.

## Before you begin

Have your existing server address, administrator password, and a recent backup available. You also need access to the computer running IPTVBoss: an SSH connection for a VPS, a terminal on a local Linux server, or the server's Windows/macOS desktop.

The command walkthroughs below assume an Ubuntu/Linux server. For native Windows or macOS services, use the same connection-mode settings with the [Windows](../server/setup/windows.md) or [macOS](../server/setup/macos-service.md) file-editing and restart procedures. Docker Desktop users can apply the same Compose settings using their platform's text editor and terminal.

??? info "Command reference: example values, SSH, and saving files"

    The examples below use these names. Replace them with your own values:

    | Example | What it means |
    | --- | --- |
    | `boss.example.com` | Your existing public server hostname. |
    | `192.168.1.50` | Your server's private LAN address. |
    | `8001` | The default XC Server port. Keep your existing port if it is different. |
    | `iptvboss.service` | The systemd service from the Linux installation guide. Use your actual service name if different. |
    | `~/iptvboss-xc` | The Compose directory from the Docker guide. Use your existing directory, not a new one. |

    Run Linux service and Docker commands **on the server**, not on the desktop you will pair later. For a VPS, open a terminal on your computer and connect using your usual SSH command. If you need help connecting, see [Connect to the VPS](../server/setup/prepare-vps.md#4-connect-to-the-vps-for-the-first-time).

    When a step opens `nano`, edit the file, press **Ctrl+O**, then **Enter** to save, and **Ctrl+X** to exit. `sudo` may ask for your Linux account password; nothing appears while you type it.

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

Expand **only the path that matches your setup**. Each section contains its configuration steps. Keep your existing hostname and port, then continue to [Restart and verify](#restart-and-verify).

| Your setup | Follow this section | Address clients use |
| --- | --- | --- |
| Caddy, Nginx, or another reverse proxy handles HTTPS | [Reverse proxy handles HTTPS](#reverse-proxy-handles-https) | `https://boss.example.com` |
| Devices connect directly over a trusted local network | [Direct connection on your local network](#direct-connection-on-your-local-network) | `http://192.168.1.50:8001` |
| IPTVBoss itself handles HTTPS with a certificate | [IPTVBoss handles HTTPS directly](#iptvboss-handles-https-directly) | `https://boss.example.com:8001` |

??? info "Reverse proxy handles HTTPS"

    <span id="reverse-proxy-handles-https"></span>

    In this setup, clients connect to Caddy over HTTPS. Caddy forwards the request to IPTVBoss over HTTP. Caddy handles the certificate, so IPTVBoss does not need `-httpsOnly` or a local `keystore.p12`.

    <span id="1-find-your-caddyfile"></span>

    **1. Find your Caddyfile**

    The Caddyfile is a text file belonging to Caddy, not a file in the IPTVBoss data directory. Its location depends on how you installed Caddy:

    | Installation from this guide | Caddyfile location |
    | --- | --- |
    | Caddy installed as an Ubuntu/Linux system service | `/etc/caddy/Caddyfile` |
    | Caddy bundled in Docker Compose | `Caddyfile` beside your existing `compose.yaml`, normally `~/iptvboss-xc/Caddyfile` on the host. Compose mounts it at `/etc/caddy/Caddyfile` inside the Caddy container. |
    | Caddy installed with Homebrew on macOS | `etc/Caddyfile` under the Homebrew prefix; see [macOS Caddy setup](../server/setup/macos-service.md#5-install-caddy). |
    | Caddy installed using the Windows guide | `C:\Caddy\Caddyfile`; see [Windows setup](../server/setup/windows.md). |

    The next two steps cover **native Ubuntu/Linux services**. Docker users should follow [step 4](#4-update-a-docker-proxy-setup) instead. For an existing Nginx or other proxy, keep its own configuration file and make sure it forwards to the correct HTTP backend with `X-Forwarded-Proto: https`.

    <span id="2-update-the-native-ubuntu-service-command"></span>

    **2. Update the native Ubuntu service command**

    Run **on the server**:

    ```bash
    sudo systemctl cat iptvboss.service
    ```

    This displays the service definition and any overrides, with their file paths. Find `ExecStart`, `User`, any `-directory` or `-xc-port` arguments, and any `Environment` or `EnvironmentFile` entries. Record them before changing anything. Keeping the same user and data directory ensures the updated server opens your existing database.

    Open an override for the existing service:

    ```bash
    sudo env SYSTEMD_EDITOR=nano systemctl edit iptvboss.service
    ```

    Add the following **above the editor's discard marker**. If the override already has these settings, edit those entries instead of adding another copy, and preserve unrelated settings. Use your existing executable path, and copy any existing `-directory` and `-xc-port` arguments onto the new `ExecStart` line:

    ```ini
    [Service]
    ExecStart=
    ExecStart=/usr/bin/iptvboss -xcserver -xc-proxy -xc-bind-address loopback
    ```

    For example, if your current command contains `-directory /srv/iptvboss`, the last line becomes:

    ```ini
    ExecStart=/usr/bin/iptvboss -xcserver -xc-proxy -xc-bind-address loopback -directory /srv/iptvboss
    ```

    The empty `ExecStart=` clears the old command before the next line replaces it. Do not change `User` as part of this upgrade. Remove `-httpsOnly` from the replacement command. If the old service sets `IPTVBOSS_HTTPS_ONLY=true`, clear it in the file shown by `systemctl cat`; if it uses an `EnvironmentFile`, edit that file as well. Proxy mode takes precedence, but keeping only the intended mode avoids confusion later.

    The command selects nano as the editor. Save with **Ctrl+O**, **Enter**, then **Ctrl+X**. `systemctl edit` normally saves the override under `/etc/systemd/system/iptvboss.service.d/override.conf`.

    Check the saved service definition:

    ```bash
    sudo systemctl cat iptvboss.service
    ```

    Confirm that the final `ExecStart` includes proxy mode, loopback binding, and your original data-directory and port arguments. Do not start a second `iptvboss` process manually; the service will be restarted in [Restart and verify](#restart-and-verify).

    <span id="3-edit-and-check-the-native-caddyfile"></span>

    **3. Edit and check the native Caddyfile**

    First confirm which file the existing Caddy service loads:

    ```bash
    sudo systemctl cat caddy
    ```

    Look for the `--config` path. The [standard Caddy service](https://caddyserver.com/docs/running#using-the-service) uses `/etc/caddy/Caddyfile`; substitute the displayed path below if your installation differs. If the service is not found, check whether Caddy runs in Docker before installing anything. For a new native Caddy installation, follow [Linux Caddy setup](../server/setup/linux-service.md#put-caddy-in-front-of-iptvboss).

    Back up the existing file, then open it **on the server**:

    ```bash
    sudo cp --backup=numbered /etc/caddy/Caddyfile /etc/caddy/Caddyfile.pre-iptvboss-update
    sudo nano /etc/caddy/Caddyfile
    ```

    Find the block for your IPTVBoss hostname. Its basic configuration should look like this:

    ```caddyfile
    boss.example.com {
        reverse_proxy 127.0.0.1:8001
    }
    ```

    Replace `boss.example.com` with your real hostname and `8001` with your XC Server port. Edit the existing hostname block rather than adding a duplicate. Preserve other sites and any settings your deployment needs. Caddy supplies the forwarded HTTPS headers automatically with this standard configuration.

    Save with **Ctrl+O**, **Enter**, then exit with **Ctrl+X**. Validate the saved file:

    ```bash
    sudo caddy validate --config /etc/caddy/Caddyfile --adapter caddyfile
    ```

    Look for `Valid configuration`. If validation reports an error, reopen the file and correct it before reloading Caddy. When it passes:

    ```bash
    sudo systemctl reload caddy
    sudo systemctl status caddy --no-pager
    ```

    Caddy should report `active (running)`. Your existing DNS should still point to this server, and TCP ports `80` and `443` must remain reachable for public HTTPS. Keep backend port `8001` closed to the Internet. Continue to [Restart and verify](#restart-and-verify).

    <span id="4-update-a-docker-proxy-setup"></span>

    **4. Update a Docker proxy setup**

    Run **on the Docker host**. Open your existing Compose directory:

    ```bash
    cd ~/iptvboss-xc
    ls -la
    ```

    You should see `compose.yaml` and `.env`. If they are missing, find the directory you used for the original installation before continuing. Do not create a new directory or replace the existing volume configuration.

    Back up and edit `.env`:

    ```bash
    cp --backup=numbered .env .env.pre-iptvboss-update
    nano .env
    ```

    For Caddy running on the Docker host or bundled in the same Compose stack, change the existing entries to:

    ```env
    IPTVBOSS_IMAGE=ghcr.io/walrusone/iptvboss-release
    IPTVBOSS_TAG=latest
    IPTVBOSS_XC_BEHIND_HTTPS_PROXY=true
    IPTVBOSS_HTTPS_ONLY=false
    IPTVBOSS_XC_BIND_ADDRESS=all
    IPTVBOSS_HOST_IP=127.0.0.1
    ```

    Keep `all` for the listener **inside the IPTVBoss container** so Caddy can reach it. `IPTVBOSS_HOST_IP=127.0.0.1` restricts the published port on the host. Save and exit nano.

    If Caddy runs as a service directly on the Docker host, edit `/etc/caddy/Caddyfile` using [step 3](#3-edit-and-check-the-native-caddyfile). Its upstream is `127.0.0.1:8001`, or your configured **host** port.

    If Caddy runs **inside the same Compose stack**, open the host-side file beside `compose.yaml`:

    ```bash
    nano Caddyfile
    ```

    The supplied template uses:

    ```caddyfile
    {$IPTVBOSS_DOMAIN} {
        reverse_proxy iptvboss:8001
    }
    ```

    Keep `{$IPTVBOSS_DOMAIN}` as written when using that template, and set `IPTVBOSS_DOMAIN=boss.example.com` in `.env` with your real hostname. Alternatively, retain your existing literal hostname in the Caddyfile. The upstream is the Compose service name `iptvboss` and its **container** port, not `127.0.0.1`, which would point back to the Caddy container itself. Keep the existing mount `./Caddyfile:/etc/caddy/Caddyfile:ro` in `compose.yaml`.

    If you are adding Caddy for the first time, follow [Bundle Caddy in the same Compose file](../server/setup/docker.md#bundle-caddy-in-the-same-compose-file) to add its service and certificate volumes. If the proxy is on another computer or a different Docker network, follow [proxies in another container or host](../server/setup/docker-advanced.md#reverse-proxies-in-another-container-or-host) instead of using host loopback.

    Continue to [Restart and verify](#restart-and-verify) to apply the changes. Review [proxy trust](../server/runtime.md#proxy-trust) if you use an explicit proxy allowlist.

??? info "Direct connection on your local network"

    <span id="direct-connection-on-your-local-network"></span>

    Use this path when devices connect directly to the server's LAN address without Caddy or another reverse proxy. HTTP does not encrypt passwords or traffic, so use it only on a trusted local network. Do not forward this port from the Internet.

    <span id="1-change-the-servers-launch-settings"></span>

    **1. Change the server's launch settings**

    For a native Ubuntu service, inspect and edit the service **on the server**:

    ```bash
    sudo systemctl cat iptvboss.service
    sudo env SYSTEMD_EDITOR=nano systemctl edit iptvboss.service
    ```

    Use this override, preserving your existing executable path and any `-directory` and `-xc-port` arguments on the new `ExecStart` line:

    ```ini
    [Service]
    ExecStart=
    ExecStart=/usr/bin/iptvboss -xcserver -xc-bind-address all
    Environment=IPTVBOSS_XC_BEHIND_HTTPS_PROXY=false
    Environment=IPTVBOSS_HTTPS_ONLY=false
    ```

    Remove `-xc-proxy` and `-httpsOnly` from the replacement command. If `systemctl cat` shows an `EnvironmentFile`, open that file and change those same two variables to `false` there too: environment-file values can override `Environment` entries. Save the override without changing the service account or data directory.

    For Docker, go to the existing Compose directory and open `.env`:

    ```bash
    cd ~/iptvboss-xc
    nano .env
    ```

    Change the existing entries to:

    ```env
    IPTVBOSS_IMAGE=ghcr.io/walrusone/iptvboss-release
    IPTVBOSS_TAG=latest
    IPTVBOSS_XC_BEHIND_HTTPS_PROXY=false
    IPTVBOSS_HTTPS_ONLY=false
    IPTVBOSS_XC_BIND_ADDRESS=all
    IPTVBOSS_HOST_IP=0.0.0.0
    ```

    Save with **Ctrl+O**, **Enter**, then exit with **Ctrl+X**. If your Compose file hardcodes environment values or command-line flags, update those too; `.env` affects only settings referenced by the Compose file.

    For an installation controlled through the desktop's saved settings, open **Settings → Server Settings** and clear **Block direct connections (bind to 127.0.0.1)**. Command-line and environment bind-address overrides take precedence over this checkbox. For other native services, edit the existing launch command using the [Windows](../server/setup/windows.md) or [macOS](../server/setup/macos-service.md) instructions.

    <span id="2-check-the-lan-address-and-firewall"></span>

    **2. Check the LAN address and firewall**

    Use the server's LAN address in the browser, for example:

    ```text
    http://192.168.1.50:8001/boss.php
    ```

    Replace the example address and port with yours. On Ubuntu, `hostname -I` lists the machine's addresses; select the LAN address reachable from your other devices. Your router's connected-device list can also help identify it.

    Allow the XC port through the server's firewall for your trusted LAN. On native Ubuntu with UFW, first inspect the existing rules:

    ```bash
    sudo ufw status verbose
    ```

    For example, **only if your trusted LAN is `192.168.1.0/24`** and the XC port is `8001`, add:

    ```bash
    sudo ufw allow from 192.168.1.0/24 to any port 8001 proto tcp
    ```

    Use your actual LAN subnet and port. [Docker-published ports can bypass UFW rules](https://docs.docker.com/engine/network/packet-filtering-firewalls/#docker-and-ufw), so do not assume UFW alone restricts them. Keep the host on the trusted LAN and do not add router port forwarding for this HTTP port.

    Continue to [Restart and verify](#restart-and-verify). From another device, use the server's LAN address, not `localhost` or `127.0.0.1`, which refer to the device making the connection.

??? info "IPTVBoss handles HTTPS directly"

    <span id="iptvboss-handles-https-directly"></span>

    Use this path when IPTVBoss itself serves HTTPS and no reverse proxy handles the certificate.

    1. Find the server's existing data directory from its launch command or service settings. For Docker using the supplied template, this is `/data` inside the container.
    2. Confirm that `keystore.p12` is available in that data directory and that `IPTVBOSS_XC_KEYSTORE_PASSWORD` supplies its password. If you need to create or mount it, follow [Direct HTTPS](../server/setup/direct-https.md) before continuing.
    3. Edit the existing launch command to include `-xcserver -httpsOnly -xc-bind-address all`, preserving the executable path, service account, data directory, and port. Remove `-xc-proxy` and set `IPTVBOSS_XC_BEHIND_HTTPS_PROXY=false` wherever it is configured. Proxy mode takes precedence if both modes are enabled.
    4. For Docker, edit the existing `.env` to set `IPTVBOSS_HTTPS_ONLY=true`, `IPTVBOSS_XC_BEHIND_HTTPS_PROXY=false`, and `IPTVBOSS_XC_BIND_ADDRESS=all`. Retain your keystore mount and password configuration from the direct-HTTPS setup.
    5. Continue with the restart steps below, then open `https://boss.example.com:8001/boss.php`, using the hostname on your certificate and your actual port. Each connecting device must trust the certificate.

## Restart and verify

### 1. Apply the changes on the server

Expand the restart instructions for your installation. For macOS or Windows services, use your [platform setup page](../server/setup/index.md).

??? info "Native Ubuntu/Linux service"

    For a **native Ubuntu service**, run:

    ```bash
    sudo systemctl daemon-reload
    sudo systemctl restart iptvboss.service
    sudo systemctl status iptvboss.service --no-pager
    sudo journalctl -u iptvboss.service --no-pager -n 50
    ```

    The service should report `active (running)`. Allow database migration to finish. If startup fails, read the log output before attempting a reset.

??? info "Docker Compose"

    For **Docker**, first check the configuration from your existing Compose directory:

    ```bash
    sudo docker compose config
    ```

    Inspect the output before continuing: it should show the intended mode, port, and existing data volume. The stable image is `ghcr.io/walrusone/iptvboss-release:latest`; if `compose.yaml` hardcodes the old image, edit it there. An exact published version tag can be used instead of `latest`. Keep any existing `-f` override-file options or project-name options in all your Compose commands.

    When the configuration is correct, apply it:

    ```bash
    sudo docker compose pull
    sudo docker compose up --detach
    sudo docker compose ps
    sudo docker compose logs --tail 50 iptvboss
    ```

    The IPTVBoss container should be running and become healthy. `up --detach` recreates containers when their configuration changes; a simple container restart does not apply changed `.env` settings. Preserve the existing volume and Compose project name, and do not run `down --volumes`.

    If you edited the Caddyfile for **bundled Docker Caddy**, validate and reload it after the container is running:

    ```bash
    sudo docker compose exec caddy caddy validate --config /etc/caddy/Caddyfile --adapter caddyfile
    sudo docker compose exec caddy caddy reload --config /etc/caddy/Caddyfile --adapter caddyfile
    sudo docker compose logs --tail 50 caddy
    ```

    Only run the reload after validation succeeds. These commands use the mounted file inside the Caddy container. For native Caddy, use `sudo systemctl reload caddy` after validation as shown above.

### 2. Check the address clients will use

Open a browser **on your computer**, then use the address matching your setup:

| Mode | Console address example |
| --- | --- |
| HTTPS reverse proxy | `https://boss.example.com/boss.php` |
| Direct LAN HTTP | `http://192.168.1.50:8001/boss.php` |
| Direct HTTPS | `https://boss.example.com:8001/boss.php` |

You should see the Server Console login. For a proxy on standard HTTPS port `443`, do not append backend port `8001` to the public URL. Proxy mode expects forwarded HTTPS information, so a direct HTTP visit to its backend is not a substitute for testing the proxy URL.

??? info "Troubleshooting: the console will not open"

    If you cannot reach the console, check the following before changing credentials or resetting the database:

    | Symptom | First check |
    | --- | --- |
    | Caddy reports a gateway error | Confirm IPTVBoss is running and that Caddy's upstream matches its port. For bundled Docker Caddy, use `iptvboss`, not `127.0.0.1`. |
    | Another LAN device cannot connect | Confirm the listener uses `all`, the address is the server's LAN address, and the firewall permits the port. |
    | A direct HTTP visit is rejected in proxy mode | Open the HTTPS hostname through the proxy and check its forwarded headers. |
    | Browser reports a certificate problem | Check the hostname, certificate trust, and Caddy logs or direct-HTTPS certificate configuration. |

    See [XC Server Troubleshooting](../server/troubleshooting.md) for more detail.

### 3. Sign in and check your existing data

1. Sign in with username `admin` and your previous administrator password, unless you have since changed those credentials.
2. Confirm your layouts, users, sources, and schedules are present.
3. [Pair your desktop with the updated server](#pair-your-desktop-with-the-updated-server), or verify that its existing pairing still works.
4. Test a player connection, playlist, and EPG using an existing XC user's credentials.

## Pair your desktop with the updated server

Pairing connects each IPTVBoss desktop installation to the XC Server for database synchronization and automatic reload requests. Your existing administrator password is used to sign in to the console; it is not a pairing code. IPTV players continue to use their XC user credentials or output links and do not follow this desktop pairing procedure.

Complete the server update and confirm that the console shows the expected database before linking the updated desktop. If **Refresh Cloud Status** already confirms a working pairing, keep it; you do not need to unlink just because you updated.

!!! warning "The server database becomes the desktop database"
    When you link to an initialized XC Server, IPTVBoss restores the server's database over the database currently open on the desktop. It creates a verified local safety backup before restoring, but you should also back up both installations beforehand. If the desktop contains newer changes that are missing from the server, cancel pairing and resolve which database should be used before continuing. Pairing does not merge the two databases.

### 1. Generate a code in the server's web console

Do this **in a browser on your computer**:

1. Open the server's console address from the table above, ending in `/boss.php`.
2. Sign in with username `admin` and your existing administrator password, unless you have since changed those credentials.
3. Open **Paired Devices** in the console navigation.
4. Unlock the section with your six-digit section PIN if prompted.
5. Select **Generate Pairing Code** and copy the code. Leave the page open while you connect the desktop.

The code expires and works only once. If it expires before you finish, generate a fresh code.

### 2. Enter the connection details in the desktop application

Do this **in the updated IPTVBoss desktop application**, not in the browser:

Use the base address matching your setup:

| Your server mode | Connection address to check |
| --- | --- |
| Reverse proxy on standard HTTPS port `443` | `https://boss.example.com` — do not include backend port `8001`. |
| Direct LAN HTTP | `http://192.168.1.50:8001` — use the server's LAN address and listening port. |
| Direct HTTPS | `https://boss.example.com:8001` — use your certificate's hostname and listening port. |

Where separate **Server Port** and **Include Port in URL** controls are shown, set them so the displayed connection address matches your setup. For example, direct LAN access on port `8001` needs that port in the address; a proxy on standard port `443` does not need the backend port included.

1. Open **Settings → Server Settings**.
2. Find the connection and pairing controls under **XC Cloud Database**.
3. Enter the server's base address using the table above, and check the displayed connection address. Do not append `/boss.php`.
4. Enter a recognizable **Client Name**, such as `Living Room PC`.
5. Paste the code into **Pairing Code**.
6. Select **Link XC Server**.

### 3. Confirm the database restore

The desktop should ask **Restore the initialized XC Server database?**

1. Confirm only when the server holds the database you want this desktop to use. Select **Cancel** if you need to preserve newer desktop changes that are missing from the server.
2. Wait for pairing and the database restore to finish. IPTVBoss creates a verified local safety backup immediately before restoring.
3. If pairing succeeds but the restore does not finish, select **Retry Current Database** instead of generating another pairing code.

If the server asks to initialize or upload a desktop database instead, stop and verify its original data directory or Docker volume. Uploading the desktop database is the [bootstrap workflow for an empty server](../server/gui-settings.md#bootstrap-an-empty-xc-server-with-gui-pairing), not the normal pairing flow for an upgraded server with existing data.

### 4. Verify pairing and synchronization

1. In the desktop's **Server Settings**, select **Refresh Cloud Status** if needed.
2. In the browser, return to **Paired Devices** and confirm your **Client Name** appears in the table.
3. In the desktop, open the **Server** menu and its dashboard. Check the connection, synchronization, and backup status.
4. Open your layouts and sources and confirm they match the server database you intended to restore.
5. Repeat these steps with a fresh pairing code for each additional desktop installation.

Pairing configures the desktop to use XC Server database synchronization and provisions its automatic reload credential. Review your [database synchronization settings](../settings/backups.md) after linking, especially if you previously used Dropbox or Google Drive. See [automatic server reloads](../server/gui-settings.md#automatic-server-reloads-from-this-client) for when desktop changes reach the server.

If an old or incorrect pairing prevents linking, back up any unpublished desktop changes before using **Unlink This Installation**, then generate a fresh code and repeat the steps above. Do not unlink a working pairing as a routine upgrade step. See [Paired Devices](../server/console/paired-devices.md) and [XC Server Recovery](../server/recovery.md) for further help.
