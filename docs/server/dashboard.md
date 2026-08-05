# Server Dashboard

The Server Dashboard is the browser-based web frontend for an XC Server. It is reached through the server address, not through a **Server → Dashboard** menu on the server host.

## Open the dashboard

Use the normal address after an API key has been configured:

```text
https://boss.somedomain.net/boss.php?apikey=apikey
```

For bootstrap, use the same path with an empty query string:

```text
https://boss.somedomain.net/boss.php?
```

Replace the hostname and API key with the values for your installation. Confirm that the dashboard reports a healthy connection and that the server is ready before making changes.

![XC Server Dashboard](../assets/images/server/server-dashboard.png)

## Pair a desktop installation

1. Open the dashboard in a browser.
2. Select **Generate Pairing Code**.
3. On the desktop installation, open **Settings** → **Server Settings**.
4. Enter the reachable **Server URL** and **Server Port**.
5. Enable **Include Port in URL** when the public address requires it.
6. Enter a recognizable **Client Name** and the temporary **Pairing Code**.
7. Select **Link XC Server**.
8. Confirm the desktop installation is paired, then verify it in [Paired Devices](console/paired-devices.md).

!!! warning
    Pairing codes, API keys, and server addresses can grant access. Treat them as credentials and do not publish them.
