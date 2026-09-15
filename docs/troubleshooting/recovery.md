# Recovery and Application Files

Start with [Restore a Database Backup](restore.md) to choose local, cloud, link, or XC Server recovery. This page links to application files and XC pairing recovery, and covers Dropbox-specific recovery steps. Recovery can replace current data, so preserve the current database before changing or deleting anything.

- <span id="application-folder-locations"></span>[Application folder locations](application-files.md#application-folder-locations)

- <span id="restore-an-xc-server-paired-code"></span>[Restore an XC Server paired code](../server/recovery.md#restore-an-xc-server-paired-code)
- <span id="unpair-when-the-xc-server-is-unavailable"></span>[Unpair when the XC Server is unavailable](../server/recovery.md#unpair-when-the-xc-server-is-unavailable)

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

Use a full reinstall only when IPTVBoss cannot open or a normal database restore does not solve the problem. Before manually deleting application data, obtain the version-specific instructions described in [Reset only after review](reset.md#reset-only-after-review).

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
