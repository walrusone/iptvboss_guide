# Server Console

The IPTVBoss Server Console is the browser-based administration interface for an XC Server. Open it from the server address in a browser; it is not a screen inside the desktop IPTVBoss application.

## Open the Server Console

Use the address supplied for your server:

```text
https://boss.somedomain.net/boss.php?apikey=apikey
```

Replace `boss.somedomain.net` with the server hostname and the final `apikey` with the server API key. During first-time bootstrap, when no API key has been configured yet, use:

```text
https://boss.somedomain.net/boss.php?
```

The browser should display the web frontend and its console navigation. Do not open **Server → Dashboard** on the server host; that instruction refers to the desktop menu and does not describe how to reach the web frontend.

!!! warning
    The API key grants access to the server. Do not publish a complete console URL in screenshots, logs, or support requests.

## Console overview

The available console sections are listed in the navigation. Use the pages in this section for the individual workflows:

- [Logs](console/logs.md) — review server activity and failures.
- [Boss Metadata](console/metadata.md) — review server metadata and administrative information.
- [Server Settings](console/settings.md) — configure server connection and schedule settings.
- [User Management](console/users.md) — manage server users.
- [Paired Devices](console/paired-devices.md) — generate pairing codes and revoke device access.
- [Link Restore](console/link-restore.md) — restore or initialize a server database from a supported link.

Some sections require an administrator unlock before they can be opened. Server operations can change the authoritative database and output for every paired device, so create a backup and confirm the intended server before editing.

## Start a server sync

1. Confirm that the intended local database has been backed up.
2. Confirm that no other editor is making changes.
3. Select **Start Sync**.
4. Wait for the synchronization status to return to an idle or successful state.
5. Review the result in [Logs](console/logs.md).

Do not close the application or edit the database while a server sync is in progress.

## Apply changes safely

Save one logical group of changes at a time. After saving, confirm the connection status, run a controlled sync or output test, review [Logs](console/logs.md), and verify that paired desktop installations can still connect.

## Server bootstrap and restore

When the server has no usable database, it may enter bootstrap mode and require a database restore before it can continue. Open the web frontend with the bootstrap URL above, then use [Link Restore](console/link-restore.md).

![Server bootstrap mode](../assets/images/server-console/bootstrap-mode.png)

!!! note
    Server sections and labels may change between releases.
