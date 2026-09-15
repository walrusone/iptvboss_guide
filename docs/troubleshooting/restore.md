# Restore a Database Backup

Restore a known-good backup when configuration or database content needs to be recovered. Before replacing data, [preserve the current installation](../settings/backups.md#preserve-the-current-desktop-installation).

## Choose the restore source

Open the desktop **Settings** menu and select **Restore Database Backup**. Choose the source that holds your database backup:

| Source | Use when |
| --- | --- |
| Local backup | The backup file is on this computer or accessible storage. |
| Cloud backup | The backup is available through the configured cloud provider. |
| Restore From Link | You have a supported download link to the backup. See [URL restore](recovery.md#restore-by-url). |
| Server backup | The backup belongs to an XC Server. A paired installation opens its server dashboard; an unpaired installation opens server settings so it can connect first. |

![Restore Database Backup choices](../assets/images/troubleshooting/restore-database-backup.png)

## Restore and verify

1. Finish operations that write to the database.
2. Confirm the backup's date and which installation it belongs to.
3. Select the backup through the matching restore option.
4. Read the confirmation and verify which current database will be replaced.
5. Allow restoration and database reload to finish.
6. Confirm that the expected sources, layouts, users, and settings are present.
7. Follow [After a successful restore](recovery.md#after-a-successful-restore) to synchronize and verify output.

For a backup removed from Dropbox, use [Recover a deleted Dropbox backup](recovery.md#restore-a-deleted-dropbox-backup). To initialize or restore a server through its browser console, see [Link Restore](../server/console/link-restore.md).

If the backup fails to restore, retain the current files and collect [Logs and Diagnostics](logs.md). Follow [Resetting IPTVBoss](reset.md) before considering manual deletion or a full reinstall.
