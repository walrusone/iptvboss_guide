# Linux and macOS XC Service Installation — Draft

!!! danger "Draft — Still Requires Final Review & Testing"
    The service definitions, file permissions, shutdown behavior, upgrades, and Caddy startup still require final platform review and hands-on testing. The downloads currently come from the Alpha channel.

The native path runs the packaged `iptvboss-c` console launcher as an operating-system service. It uses the JVM bundled with the IPTVBoss release; a separate Java installation is not required.

Download the templates before starting:

- [Caddyfile](../../assets/downloads/xc-server/Caddyfile)
- [iptvboss-xc.service](../../assets/downloads/xc-server/iptvboss-xc.service) for Linux
- [pro.iptvboss.xcserver.plist](../../assets/downloads/xc-server/pro.iptvboss.xcserver.plist) for macOS

Replace `boss.domain.com` in `Caddyfile` with the server's public hostname.

## Linux with systemd

The Alpha release archives contain `bin/iptvboss-c` for Linux AMD64 and ARM64.

### Install IPTVBoss

1. Open the [IPTVBoss Alpha releases](https://github.com/walrusone/iptvboss-alpha/releases) page.
2. Select the Alpha version being evaluated.
3. Download `iptvboss-VERSION-linux-amd64.tar.gz` for Intel/AMD x64 or `iptvboss-VERSION-linux-aarch64.tar.gz` for ARM64.
4. Verify that the filename, architecture, release source, and any published checksum match the intended release.

Create a locked service account and persistent data directory:

```bash
sudo useradd --system --home-dir /var/lib/iptvboss --create-home \
  --shell /usr/sbin/nologin iptvboss
sudo install -d -o iptvboss -g iptvboss -m 0700 /var/lib/iptvboss
```

If the `iptvboss` account already exists, do not recreate it. Extract the selected archive into `/opt/iptvboss`:

```bash
sudo install -d -o root -g root -m 0755 /opt/iptvboss
sudo tar --extract --gzip --file ./iptvboss-VERSION-linux-ARCH.tar.gz \
  --strip-components=1 --directory /opt/iptvboss
sudo chown -R root:root /opt/iptvboss
sudo chmod 0755 /opt/iptvboss/bin/iptvboss-c
```

Replace `VERSION` and `ARCH` with the downloaded asset's values.

### Install the draft service

Review the downloaded unit, then install and validate it:

```bash
sudo cp ./iptvboss-xc.service /etc/systemd/system/iptvboss-xc.service
sudo systemd-analyze verify /etc/systemd/system/iptvboss-xc.service
sudo systemctl daemon-reload
sudo systemctl enable --now iptvboss-xc
sudo systemctl status iptvboss-xc
```

The unit launches:

```text
/opt/iptvboss/bin/iptvboss-c -xcserver -directory /var/lib/iptvboss -xc-proxy -xc-bind-address loopback
```

It runs as `iptvboss`, accepts connections only on loopback, trusts forwarded headers only from loopback, restarts after unexpected failure, and allows up to 60 seconds for shutdown.

Check the service and local health endpoint:

```bash
sudo journalctl -u iptvboss-xc --no-pager -n 100
curl --fail http://127.0.0.1:8001/healthz
```

### Install Caddy on Linux

Install Caddy using its [official distribution-specific instructions](https://caddyserver.com/docs/install), then validate and install the downloaded configuration:

```bash
caddy validate --config ./Caddyfile
sudo cp ./Caddyfile /etc/caddy/Caddyfile
sudo systemctl reload caddy
sudo systemctl status caddy
curl --fail https://boss.domain.com/healthz
```

## macOS with launchd

The macOS Alpha archive contains `/Applications/IPTVBoss.app/Contents/MacOS/iptvboss-c` after the app is installed.

### Install IPTVBoss

1. Open the [IPTVBoss Alpha releases](https://github.com/walrusone/iptvboss-alpha/releases) page.
2. Download the macOS archive matching Apple Silicon (`aarch64`) or Intel (`amd64`).
3. Verify the release source and architecture, extract the archive, and move `IPTVBoss.app` to `/Applications`.
4. Confirm the console launcher exists:

    ```bash
    test -x /Applications/IPTVBoss.app/Contents/MacOS/iptvboss-c
    ```

The LaunchDaemon starts at boot as a named, non-root macOS user. Choose the account that will own the XC data, then replace every `YOUR_MAC_USERNAME` placeholder in the downloaded plist.

Create its data directory, substituting the same username:

```bash
IPTVBOSS_MAC_USER=YOUR_MAC_USERNAME
IPTVBOSS_MAC_GROUP=$(id -gn "$IPTVBOSS_MAC_USER")
sudo install -d -o "$IPTVBOSS_MAC_USER" -g "$IPTVBOSS_MAC_GROUP" -m 0700 \
  "/Users/$IPTVBOSS_MAC_USER/Library/Application Support/IPTVBoss-XC"
```

After reviewing the completed plist:

```bash
plutil -lint ./pro.iptvboss.xcserver.plist
sudo install -o root -g wheel -m 0644 ./pro.iptvboss.xcserver.plist \
  /Library/LaunchDaemons/pro.iptvboss.xcserver.plist
sudo launchctl bootstrap system /Library/LaunchDaemons/pro.iptvboss.xcserver.plist
sudo launchctl print system/pro.iptvboss.xcserver
curl --fail http://127.0.0.1:8001/healthz
```

Service output is written to `service-stdout.log` and `service-stderr.log` in the configured `IPTVBoss-XC` data directory.

### Install Caddy on macOS

Install Caddy and register its service at boot:

```bash
brew install caddy
IPTVBOSS_CADDY_PREFIX=$(brew --prefix)
caddy validate --config ./Caddyfile
sudo cp ./Caddyfile "$IPTVBOSS_CADDY_PREFIX/etc/Caddyfile"
sudo brew services start caddy
sudo brew services list
curl --fail https://boss.domain.com/healthz
```

Running `brew services` with `sudo` creates a system LaunchDaemon rather than a login-only LaunchAgent. This configuration remains subject to the final macOS review and test pass.

## Back up and upgrade native installations

The data directory is the durable state:

- Linux: `/var/lib/iptvboss`
- macOS: `/Users/YOUR_MAC_USERNAME/Library/Application Support/IPTVBoss-XC`

Before changing versions, stop the XC service and make a verified backup of the complete data directory.

=== "Linux"

    ```bash
    sudo systemctl stop iptvboss-xc
    ```

    After the backup, replace the contents of `/opt/iptvboss` with the new matching-architecture archive, restore root ownership and executable permissions, then run:

    ```bash
    sudo systemctl start iptvboss-xc
    sudo journalctl -u iptvboss-xc --no-pager -n 100
    ```

=== "macOS"

    ```bash
    sudo launchctl bootout system/pro.iptvboss.xcserver
    ```

    After the backup, replace `/Applications/IPTVBoss.app` with the new matching-architecture app, then run:

    ```bash
    sudo launchctl bootstrap system /Library/LaunchDaemons/pro.iptvboss.xcserver.plist
    sudo launchctl print system/pro.iptvboss.xcserver
    ```

Check local and public health before signing in. For a rollback, stop the service, restore the matching pre-upgrade data backup if the schema changed, restore the previous application version, and start the service again.

Continue with [first-time setup](index.md#after-installation) after both health checks succeed.
