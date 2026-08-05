# Recovery and Application Files

This page covers common application locations and a Dropbox-based recovery workflow. Recovery can replace current data, so preserve the current database before changing or deleting anything.

## Application folder locations

| Operating system | Default location |
| --- | --- |
| Windows | `C:\Users\{user}\IPTVBoss` |
| Linux | `/home/{user}/IPTVBoss` |
| macOS | `~/Library/Application Support/IPTVBoss/` |

When Windows and headless Linux use the same synchronized installation data, the Windows drive path may appear under the Linux application folder, for example:

```text
/home/{user}/IPTVBoss/C:
```

If the Windows installation uses another drive or custom path, use the corresponding folder name instead of `C:`.

The `db` and `backup` directories are especially important. Do not delete them until you have copied them somewhere safe and confirmed that the copy can be read.

## Restore an XC Server paired code

When an installation is reinstalled, moved to another computer, or loses its local XC Server pairing, use **XC Server Paired Code Restore** when the server provides a paired recovery code. This restores the installation’s connection to the existing XC Server without creating a new server or replacing the server database.

Before starting, confirm that you are using the intended XC Server and that the current server database is backed up. A paired code grants access to that server and should be handled like a temporary credential.

1. Open the [Server Console](../server/index.md) in a browser, then open [Paired Devices](../server/console/paired-devices.md).
2. Generate or copy the paired recovery code for the installation.
3. On the installation being recovered, open **Settings** → **Server Settings**.
4. Open **XC Server Paired Code Restore**.
5. Enter the server address when requested, then paste the paired code.
6. Apply the restore and wait for the installation to report that it is paired.
7. Confirm the installation in the server’s **Paired Devices** list.
8. Review **XC Cloud Database** settings and confirm that synchronization is enabled only when the server database should be authoritative.

!!! warning
    Do not use paired code restore to initialize an unrelated server or to replace a database. If the server is already paired to other installations, confirm the server URL and database owner before applying the code.

If the paired code is expired or rejected, generate a new code and repeat the restore. For the normal first-time pairing flow, see [GUI Server Dashboard and Desktop GUI Settings](../server/settings.md).

## Restore a deleted Dropbox backup

Dropbox retains deleted files for a limited period according to the Dropbox account and plan. A noGUI run may create a database backup, so older backup files can sometimes be recovered from Dropbox deleted items.

1. Open Dropbox deleted files.
2. Search backward through the available deleted IPTVBoss database backups.
3. Choose a backup from a date when the configuration was known to work.
4. Use one of the restore methods below.

The Dropbox deleted-files page and controls may change. If a direct file URL is available, change its download parameter from `dl=0` to `dl=1` before using it as a restore link.

!!! warning
    A restore replaces current database data. Close IPTVBoss, preserve the current `db` and `backup` directories, and confirm the selected backup before restoring.

## Restore by URL

When the Dropbox file URL can be copied:

1. Open IPTVBoss.
2. Select **Settings** → **Restore database**.
3. Choose **URL restore**.
4. Paste the Dropbox file URL.
5. If necessary, change the URL ending from `&dl=0` to `&dl=1`.
6. Select **OK** or **Restore**.
7. Wait for the restore to finish; large backups may take several minutes.

## Restore through Dropbox cloud sync

If the current Dropbox interface does not provide a usable file URL:

1. In Dropbox, open **Deleted items**.
2. Select the IPTVBoss database backup and choose **Restore**.
3. Restore it to the main synchronized folder.
4. In IPTVBoss, open **IPTVBoss Settings** → **Restore**.
5. Choose **Restore from cloud**.
6. Select the restored backup and wait for the operation to finish.

Repeat with another dated backup if the first one does not resolve the problem.

## Full reinstall and restore

Use a full reinstall only when IPTVBoss cannot open or a normal database restore does not solve the problem.

1. Close IPTVBoss and stop any noGUI or server process.
2. Move the contents of the application `db` and `backup` directories to a safe location.
3. If Dropbox is synchronizing the application database, remove the broken database from the sync workflow only after preserving a local copy.
4. Uninstall IPTVBoss.
5. Delete the old application folder only after confirming that the backup copy is safe.
6. Restart the computer.
7. Install a fresh copy of IPTVBoss.
8. Open IPTVBoss once, then close it.
9. Restore a known-good Dropbox database using [URL restore](#restore-by-url) or [Dropbox cloud sync](#restore-through-dropbox-cloud-sync).

!!! danger
    Deleting the application folder can permanently remove the only usable database. If you cannot verify a backup, stop and collect clean logs before deleting anything.

## After a successful restore

Complete these steps in order:

1. Open IPTVBoss and confirm the restored sources and layouts.
2. Run **Sources** → **Sync All EPGs**.
3. Run **Sources** → **Sync All Sources**.
4. If cloud output is used, open **IPTVBoss Settings** → **Authorize Cloud** and complete authorization.
5. Run **Output** → **All Layouts & EPGs** and wait for completion.
6. Sync the affected player or client.
7. Confirm that sources, EPG data, layouts, users, and output links are correct.
8. Recreate or review the noGUI schedule.
9. Close IPTVBoss so the restored database can finish synchronizing to the cloud provider.

If the restore still fails, stop repeating destructive steps and provide [clean IPTVBoss logs](logs.md), the operating system, the application version, and the restore method used.
