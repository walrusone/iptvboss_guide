# XC Server Overview

--8<-- "includes/xc-server-pro.md"

IPTVBoss can run as an XC Server without opening the desktop application. In this mode it runs continuously, provides the browser-based [Server Console](index.md), and serves XC playlists, EPG data, and streams.

For existing installations, use [server troubleshooting](troubleshooting.md) or [server recovery](recovery.md).

XC Server mode is normally run behind an HTTPS reverse proxy. IPTVBoss listens on the local machine, while the reverse proxy handles the public hostname, TLS certificate, and Internet-facing connection.

!!! warning "Protect remote connections"
    Direct HTTP is not encrypted. Do not expose a direct HTTP XC Server to the Internet. Use an HTTPS reverse proxy or configure direct HTTPS before allowing remote access.

Start with [First XC Server Connection](first-connection.md) for installation, database initialization, desktop pairing, and a player test. Use [Runtime Configuration](runtime.md) for command-line flags, ports, environment variables, and HTTPS modes.

## Configuration reference

- <span id="recommended-command"></span>[Recommended command](runtime.md#recommended-command)
- <span id="xc-server-flags"></span>[XC Server flags](runtime.md#xc-server-flags)
- <span id="data-and-port-defaults"></span>[Data and port defaults](runtime.md#data-and-port-defaults)
- <span id="listener-selection"></span>[Listener selection](runtime.md#listener-selection)
- <span id="https-modes"></span>[HTTPS modes](runtime.md#https-modes)
- <span id="https-reverse-proxy"></span>[HTTPS reverse proxy](runtime.md#https-reverse-proxy)
- <span id="direct-http"></span>[Direct HTTP](runtime.md#direct-http)
- <span id="direct-https"></span>[Direct HTTPS](runtime.md#direct-https)
- <span id="xc-server-environment-variables"></span>[XC Server environment variables](runtime.md#xc-server-environment-variables)
- <span id="proxy-trust"></span>[Proxy trust](runtime.md#proxy-trust)

- <span id="reset-the-xc-administrator"></span>[Reset the XC administrator](recovery.md#reset-the-xc-administrator)
