# Install the XC Server as a macOS Service

--8<-- "includes/xc-server-preview.md"

!!! warning "Needs macOS testing"
    This procedure and its launchd template still need hands-on testing on both Apple Silicon and Intel macOS. Treat it as untested guidance until those checks are complete.

This setup runs the packaged `iptvboss-c` console launcher without the desktop interface. macOS starts it at boot with launchd, and Caddy provides the public HTTPS address.

The IPTVBoss package includes its own Java runtime. A separate Java installation is not required.

## 1. Prepare the hostname and network

Choose a hostname such as `boss.example.com`. Before continuing:

1. Create a DNS `A` record that points the hostname to your public IPv4 address. Add an `AAAA` record only when the Mac has working public IPv6.
2. Forward TCP ports `80` and `443` from the router to the Mac.
3. Confirm that another application is not already using ports `80` or `443`.
4. If the macOS firewall is enabled, allow incoming connections for Caddy.
5. Make sure the Mac will remain powered on and will not sleep while it is serving clients.

Caddy uses ports `80` and `443` to obtain and renew the public HTTPS certificate. IPTVBoss itself remains on `127.0.0.1:8001` by default and is not exposed directly to the network. Use `IPTVBOSS_XC_PORT` in the launchd environment or `-xc-port` in `ProgramArguments` to select another listener port.

## 2. Install IPTVBoss

1. Open the [official IPTVBoss download page](https://walrusone.github.io/iptvboss-release/download.html).
2. Download the macOS installer for Apple Silicon or Intel, matching the Mac's processor.
3. Install `IPTVBoss.app` in `/Applications`.
4. Open IPTVBoss once from Applications and approve the normal macOS security prompt only if the installer came from the official download page.
5. Quit IPTVBoss before configuring the background service.

Confirm that the console launcher exists:

```bash
test -x /Applications/IPTVBoss.app/Contents/MacOS/iptvboss-c
```

No output means the check succeeded. If it reports an error, confirm the application is named `IPTVBoss.app` and is installed directly in `/Applications`.

## 3. Prepare the data directory and launchd file

Download [pro.iptvboss.xcserver.plist](../../assets/downloads/xc-server/pro.iptvboss.xcserver.plist) into a working directory, such as Downloads.

Find the short name of the current macOS account:

```bash
id -un
```

Open the downloaded plist in a text editor and replace every occurrence of `YOUR_MAC_USERNAME` with that exact short name. Do not replace it with the account's display name.

Create the service data directory:

```bash
IPTVBOSS_MAC_USER=$(id -un)
IPTVBOSS_MAC_GROUP=$(id -gn "$IPTVBOSS_MAC_USER")
sudo install -d -o "$IPTVBOSS_MAC_USER" -g "$IPTVBOSS_MAC_GROUP" -m 0700 \
  "/Users/$IPTVBOSS_MAC_USER/Library/Application Support/IPTVBoss-XC"
```

This directory stores the database, generated files, caches, configuration, and service logs. The plist passes it to IPTVBoss with `-directory`.

## 4. Install and start the launchd service

In Terminal, change to the directory containing the edited plist. Validate it before installation:

```bash
plutil -lint ./pro.iptvboss.xcserver.plist
```

The result should end with `OK`. Install and start it:

```bash
sudo install -o root -g wheel -m 0644 ./pro.iptvboss.xcserver.plist \
  /Library/LaunchDaemons/pro.iptvboss.xcserver.plist
sudo launchctl bootstrap system /Library/LaunchDaemons/pro.iptvboss.xcserver.plist
sudo launchctl print system/pro.iptvboss.xcserver
```

Check the local health endpoint:

```bash
curl --fail http://127.0.0.1:8001/healthz
```

Replace `8001` with the configured XC port when an override is in use.

View recent service output when the health check fails:

```bash
tail -n 100 "$HOME/Library/Application Support/IPTVBoss-XC/service-stdout.log"
tail -n 100 "$HOME/Library/Application Support/IPTVBoss-XC/service-stderr.log"
```

The plist runs IPTVBoss as the selected normal user, enables reverse-proxy mode, and restricts the listener to loopback. It restarts after an unexpected failure, allows 60 seconds for shutdown, and remains stopped after a clean exit.

## 5. Install Caddy

Install [Homebrew](https://brew.sh/) if it is not already available, then install Caddy:

```bash
brew install caddy
```

Find Homebrew's configuration directory and open its Caddyfile:

```bash
IPTVBOSS_CADDY_PREFIX=$(brew --prefix)
sudo nano "$IPTVBOSS_CADDY_PREFIX/etc/Caddyfile"
```

Enter this configuration, replacing the example hostname:

```caddyfile
boss.example.com {
    reverse_proxy 127.0.0.1:8001
}
```

Save with <kbd>Ctrl</kbd>+<kbd>O</kbd>, press <kbd>Enter</kbd>, and exit with <kbd>Ctrl</kbd>+<kbd>X</kbd>. Validate the configuration:

```bash
caddy validate --config "$IPTVBOSS_CADDY_PREFIX/etc/Caddyfile" --adapter caddyfile
```

Register Caddy as a system service so it starts at boot, not only after this user logs in:

```bash
IPTVBOSS_BREW=$(command -v brew)
sudo "$IPTVBOSS_BREW" services start caddy
sudo "$IPTVBOSS_BREW" services list
```

Using `sudo` with `brew services` registers a system LaunchDaemon. Without `sudo`, Homebrew registers a per-user LaunchAgent that starts only at login.

## 6. Open the Server Console

Allow a short time for Caddy to obtain the first certificate, then test the public address:

```bash
curl --fail https://boss.example.com/healthz
```

Open the Server Console:

```text
https://boss.example.com/boss.php
```

On a new installation, the first visit creates the administrator account. Continue with [first-time setup](index.md#after-installation).

## Service commands

Restart IPTVBoss after changing its launchd configuration:

```bash
sudo launchctl bootout system/pro.iptvboss.xcserver
sudo launchctl bootstrap system /Library/LaunchDaemons/pro.iptvboss.xcserver.plist
```

Stop and unload IPTVBoss:

```bash
sudo launchctl bootout system/pro.iptvboss.xcserver
```

Load it again:

```bash
sudo launchctl bootstrap system /Library/LaunchDaemons/pro.iptvboss.xcserver.plist
```

After changing Caddy's configuration, validate it and restart the Homebrew service:

```bash
IPTVBOSS_CADDY_PREFIX=$(brew --prefix)
IPTVBOSS_BREW=$(command -v brew)
caddy validate --config "$IPTVBOSS_CADDY_PREFIX/etc/Caddyfile" --adapter caddyfile
sudo "$IPTVBOSS_BREW" services restart caddy
```

## Back up and upgrade

Stop IPTVBoss before copying its data or replacing the application:

```bash
sudo launchctl bootout system/pro.iptvboss.xcserver
```

Create a dated backup, replacing the example date:

```bash
mkdir -p "$HOME/IPTVBoss-XC-backups"
ditto "$HOME/Library/Application Support/IPTVBoss-XC" \
  "$HOME/IPTVBoss-XC-backups/2026-08-23"
```

Copy the backup to another computer or storage device. Install the IPTVBoss update in `/Applications`, verify that `iptvboss-c` still exists, and load the service again:

```bash
test -x /Applications/IPTVBoss.app/Contents/MacOS/iptvboss-c
sudo launchctl bootstrap system /Library/LaunchDaemons/pro.iptvboss.xcserver.plist
sudo launchctl print system/pro.iptvboss.xcserver
```

Check both health addresses and sign in after the upgrade.

## Troubleshooting

| Problem | What to check |
| --- | --- |
| `bootstrap failed: 5` or the job exits immediately | Run `plutil -lint`, confirm every username placeholder was replaced, and inspect both service log files. |
| The local health check fails | Confirm `iptvboss-c` exists, the data directory belongs to the configured user, and `launchctl print` shows the job. |
| Caddy does not start | Validate the host Caddyfile and inspect `sudo brew services list`. Confirm no other application owns ports `80` or `443`. |
| Caddy cannot obtain a certificate | Confirm public DNS and router forwarding for TCP ports `80` and `443`. |
| The service no longer starts after an IPTVBoss update | Confirm the application is still installed as `/Applications/IPTVBoss.app` and the console launcher path has not changed. |

If a reverse proxy cannot be used, follow the [Direct HTTPS](direct-https.md) certificate and launchd guidance. That alternative remains subject to the same macOS testing warning.
