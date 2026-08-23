# macOS XC Server Installation

!!! warning "Needs macOS testing"
    This page and its launchd template still need hands-on macOS testing. Treat the procedure as evaluation guidance until it has been verified on both Apple Silicon and Intel macOS.

The macOS service runs the packaged `iptvboss-c` console launcher without the desktop interface. It uses the JVM bundled with the IPTVBoss release; a separate Java installation is not required.

Download [pro.iptvboss.xcserver.plist](../../assets/downloads/xc-server/pro.iptvboss.xcserver.plist) before starting.

## Install IPTVBoss

1. Open the [IPTVBoss Alpha releases](https://github.com/walrusone/iptvboss-alpha/releases) page.
2. Download the macOS archive matching Apple Silicon (`aarch64`) or Intel (`amd64`).
3. Verify the release source and architecture, extract the archive, and move `IPTVBoss.app` to `/Applications`.
4. Confirm the console launcher exists:

    ```bash
    test -x /Applications/IPTVBoss.app/Contents/MacOS/iptvboss-c
    ```

The downloaded plist uses a normal named macOS user. Replace every `YOUR_MAC_USERNAME` placeholder with the account that will own and run the service.

Create its data directory, substituting the same username:

```bash
IPTVBOSS_MAC_USER=YOUR_MAC_USERNAME
IPTVBOSS_MAC_GROUP=$(id -gn "$IPTVBOSS_MAC_USER")
sudo install -d -o "$IPTVBOSS_MAC_USER" -g "$IPTVBOSS_MAC_GROUP" -m 0700 \
  "/Users/$IPTVBOSS_MAC_USER/Library/Application Support/IPTVBoss-XC"
```

The plist’s `-directory` argument controls where IPTVBoss stores its database and generated files. Change it if you want to use another location, and ensure the selected user can write there.

## Configure launchd

After reviewing the completed plist, validate and install it:

```bash
plutil -lint ./pro.iptvboss.xcserver.plist
sudo install -o root -g wheel -m 0644 ./pro.iptvboss.xcserver.plist \
  /Library/LaunchDaemons/pro.iptvboss.xcserver.plist
sudo launchctl bootstrap system /Library/LaunchDaemons/pro.iptvboss.xcserver.plist
sudo launchctl print system/pro.iptvboss.xcserver
curl --fail http://127.0.0.1:8001/healthz
```

Service output is written to `service-stdout.log` and `service-stderr.log` in the configured data directory.

## Put Caddy in front of IPTVBoss

Install and register Caddy at boot:

```bash
brew install caddy
IPTVBOSS_CADDY_PREFIX=$(brew --prefix)
caddy validate --config ./Caddyfile
sudo cp ./Caddyfile "$IPTVBOSS_CADDY_PREFIX/etc/Caddyfile"
sudo brew services start caddy
sudo brew services list
```

Use the same Caddyfile and hostname setup described in [the Linux Caddy section](linux-service.md#put-caddy-in-front-of-iptvboss). Running `brew services` with `sudo` creates a system LaunchDaemon rather than a login-only LaunchAgent.

## Back up and upgrade

The data directory is the durable state. Stop the service before making a backup or replacing the application:

```bash
sudo launchctl bootout system/pro.iptvboss.xcserver
```

After the backup or upgrade, start it again:

```bash
sudo launchctl bootstrap system /Library/LaunchDaemons/pro.iptvboss.xcserver.plist
sudo launchctl print system/pro.iptvboss.xcserver
```

Continue with [first-time setup](index.md#after-installation) after the local and public health checks succeed.
