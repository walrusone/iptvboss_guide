# Server Console (3.11.43)

💲 [Pro feature — see Free vs Pro](../getting-started/free-vs-pro.md).

The IPTVBoss Server Console is the browser-based administration interface for an XC Server. The workflows below describe the 3.11.43 interface. Open it from the server address in a browser; it is not a screen inside the desktop IPTVBoss application.

## Open the Server Console

Use the address supplied for your server:

```text
https://boss.somedomain.net/boss.php?apikey=apikey
```

Replace `boss.somedomain.net` with the server hostname and the final `apikey` with the server API key. During first-time bootstrap, when no API key has been configured yet, use:

```text
https://boss.somedomain.net/boss.php?
```

The browser should display the login or setup page. On the first visit, create the administrator account and a six-digit section PIN. On later visits, sign in with the administrator account; if two-factor authentication is enabled, enter the authenticator or a recovery code as the second step.

![Server Console overview](../assets/images/server-console/server-console.png)

!!! warning
    The API key grants access to the server. Do not publish a complete console URL in screenshots, logs, or support requests.

## Console overview

The available console sections are listed in the navigation. Use the pages in this section for the individual workflows:

- [Logs](console/logs.md) — review server activity and failures.
- [Login and Setup](console/login.md) — create the first administrator and sign in.
- [Boss Metadata](console/metadata.md) — review server metadata and administrative information.
- [Server Settings](console/settings.md) — configure server connection and schedule settings.
- [User Management](console/users.md) — manage server users.
- [Paired Devices](console/paired-devices.md) — generate pairing codes and revoke device access.
- [Link Restore](console/link-restore.md) — restore or initialize a server database from a supported link.
- [API Keys](console/api.md) — rotate the XC Reload key and manage external automation keys.
- [Swagger API Documentation](console/swagger.md) — inspect the documented external API.
- [Console Security](console/security.md) — configure two-factor authentication and section PIN protection.
- [Administrator Account](console/administrator.md) — change the console username or password.

Some sections require a section-PIN unlock before they can be opened. Editing also requires ownership of the console editor lease when another desktop or console is using the database. Server operations can change the authoritative database and output for every paired device, so create a backup and confirm the intended server before editing.

## Start a server sync

1. Confirm that the intended local database has been backed up.
2. Confirm that no other editor is making changes.
3. Select **Start Sync**.
4. Wait for the synchronization status to return to an idle or successful state.
5. Review the result in [Logs](console/logs.md).

Do not close the application or edit the database while a server sync is in progress.

## Apply changes safely

1. Select **Begin Editing** before making database changes.
2. Save one logical group of changes at a time.
3. Wait for any backup or synchronization operation to finish.
4. Confirm the connection status, run a controlled sync or output test, and review [Logs](console/logs.md).
5. Select **End Editing** when finished so another editor can work.

If another editor owns the lease, the console is read-only. Use **Take Over** only after confirming that the other editor is no longer active.

## Server bootstrap and restore

When the server has no usable database, it enters bootstrap mode. Open the web frontend with the bootstrap URL above, then choose one setup path:

- Use [GUI pairing](gui-settings.md#bootstrap-an-empty-xc-server-with-gui-pairing) to push the database currently open in an IPTVBoss Pro desktop installation.
- Use [Link Restore](console/link-restore.md) to initialize the server from a supported backup link.

![Server bootstrap mode](../assets/images/server-console/bootstrap-mode.png)

!!! note
    Server sections and labels may change between releases.
