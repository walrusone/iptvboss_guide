# Server Console

The IPTVBoss Server Console is the browser-based administration interface for monitoring and operating an XC Server. It provides status, synchronization, logs, metadata, users, paired devices, and restore controls.

![Server menu](../assets/images/server-menu.png)

!!! warning
    Server operations can change the authoritative database and output for every paired device. Create a backup and confirm the intended server before editing.

## Open the console

1. Open the **Server** menu.
2. Select **Dashboard** to open the Server Console.
3. Review the server version, synchronization status, refresh status, and current mode.
4. Select **Reload Server** when you need to refresh the displayed server state.

![The Server Console](../assets/images/server-console/server-console.png)

The console is read-only until you select **Begin Editing**. Editing controls are protected because server changes affect the shared database.

## Console sections

The console can provide these sections:

- **Logs** — Review server activity and failures.
- **Boss Metadata** — Review server metadata and administrative information.
- **Server Settings** — Configure the server URL, port, and schedules.
- **User Management** — Review or manage server users.
- **Paired Devices** — Generate pairing codes and revoke device access.
- **Link Restore** — Restore or initialize a server database from a supported link workflow.

Some sections require an administrator unlock before they can be opened.

![Server logs](../assets/images/server-console/server-logs.png)

![Boss metadata](../assets/images/server-console/server-metadata.png)

![Server users](../assets/images/server-console/server-users.png)

## Start a server sync

1. Confirm that the intended local database has been backed up.
2. Confirm that no other editor is making changes.
3. Select **Start Sync**.
4. Wait for the synchronization status to return to an idle or successful state.
5. Review the result in **Logs**.

Do not close the application or edit the database while a server sync is in progress.

![Paired devices](../assets/images/server-console/paired-devices.png)

Use **Paired Devices** after setup to review connected clients or revoke a device that should no longer access the server. Treat pairing codes as temporary credentials and do not publish them. For the initial setup and pairing flow, see [Server Dashboard and GUI Settings](settings.md).

## Server bootstrap and restore

When the server has no usable database, it may enter bootstrap mode and require a database restore before it can continue.

![Server bootstrap mode](../assets/images/server-console/bootstrap-mode.png)

![Server link restore](../assets/images/server-console/link-restore.png)

Use the documented restore path and confirm which database will become authoritative. If the server is already paired, do not initialize a replacement database without owner approval.

!!! note
    Server console sections and labels may change between releases.
