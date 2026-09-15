# XC Server Recovery

--8<-- "includes/xc-server-preview.md"

Choose the procedure that matches the problem. Administrator recovery, desktop pairing, and database replacement affect different parts of the installation.

## Reset the XC administrator

Use the reset flag only when the administrator identity must be recovered:

```bash
iptvboss -xcserver -xc-reset-admin -directory /path/to/data
```

Stop every IPTVBoss process using that data directory first. The command refuses to run while the data is locked, asks for `RESET XC ADMIN`, and then clears the XC administrator identity, MFA, and trusted sessions. It preserves users, sources, layouts, settings, and backups. Start XC Server normally afterward; the console will ask you to create new administrator credentials.

For first-time initialization and normal administration, continue to the [Server Console](index.md). For installation and service management, use the [XC Server setup overview](setup/index.md).

## Repair a server left in bootstrap mode

If a paired XC Server loses its database and returns to bootstrap mode, open **Settings** → **Server Settings** and select **Refresh Cloud Status**. The desktop application reports that the server is awaiting database bootstrap and keeps cloud editing read-only until the situation is resolved.

Select **Repair Bootstrap Server** to open the recovery choices. On a Pro installation, the available recovery path can publish the database currently open in the GUI as the authoritative server database. Review the paired-client list before confirming and leave selected only the clients that should remain trusted. The current GUI cannot be removed from that retention list.

The recovery actions have different consequences:

- **Reset Server, Keep Pairing** uploads the current GUI database and retains the selected paired clients. Use this only when the open GUI database is the authoritative copy.
- **Unpair and Re-pair** is intended to remove this GUI’s local pairing while leaving the server identity and bootstrap state available for a new pairing code. If this option is not shown in the installed build, use **Unlink This Installation**, then pair again from the server’s one-time code.
- **Reset Server and Unpair** invalidates the server’s pairing state and backup history and requires a full setup again. It requires typing `RESET`.
- **Not Now** leaves the server untouched and keeps cloud editing read-only.

Do not use a reset action to recover an unrelated server. Back up the current GUI database first, verify the server address, and confirm which database should be authoritative.

## Restore an XC Server paired code

<span class="pro-badge">PRO</span> This recovery workflow applies to XC Server.

When an installation is reinstalled, moved to another computer, or loses its local XC Server pairing, use **XC Server Paired Code Restore** when the server provides a paired recovery code. This restores the installation’s connection to the existing XC Server without creating a new server or replacing the server database.

Before starting, confirm that you are using the intended XC Server and that the current server database is backed up. A paired code grants access to that server and should be handled like a temporary credential.

1. Open the [Server Console](index.md) in a browser, then open [Paired Devices](console/paired-devices.md).
2. Generate or copy the paired recovery code for the installation.
3. On the installation being recovered, open **Settings** → **Server Settings**.
4. Open **XC Server Paired Code Restore**.
5. Enter the server address when requested, then paste the paired code.
6. Apply the restore and wait for the installation to report that it is paired.
7. Confirm the installation in the server’s **Paired Devices** list.
8. Review **XC Cloud Database** settings and confirm that synchronization is enabled only when the server database should be authoritative.

!!! warning
    Do not use paired code restore to initialize an unrelated server or to replace a database. If the server is already paired to other installations, confirm the server URL and database owner before applying the code.

If the pairing code is expired or rejected, generate a new code and repeat the restore. For the normal first-time pairing flow, see [Bootstrap an empty XC Server with GUI pairing](gui-settings.md#bootstrap-an-empty-xc-server-with-gui-pairing).

## Unpair when the XC Server is unavailable

Open **Settings** → **Server Settings** and select **Unlink This Installation**. IPTVBoss stops the local XC heartbeat, attempts to revoke the server-side client, and removes the local pairing even if the server is unreachable. The status message identifies when the old server-side pairing may remain until the server can be contacted.

If the server is still in bootstrap mode, use [Repair a server left in bootstrap mode](recovery.md#repair-a-server-left-in-bootstrap-mode). Choose a local unpair/re-pair action when the server database and identity must remain intact. Use the full reset only when all XC pairing and backup history should be erased.

## Restore database content

Use [Link Restore](console/link-restore.md) for a supported backup URL, [Restore a Database Backup](../troubleshooting/restore.md) for desktop restore choices, or the [platform backup procedures](../settings/backups.md#back-up-or-update-an-xc-server) for an installation-data restore.
