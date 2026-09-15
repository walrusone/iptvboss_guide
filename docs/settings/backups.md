# Database Sync and Backups

Output files, database synchronization, and backups serve different purposes:

| What you need | Use |
| --- | --- |
| Playlist and EPG links for a player | [Cloud output publishing](cloud-providers.md) |
| Share the working database between installations | Database synchronization through a configured cloud provider or [XC Server](../server/gui-settings.md) |
| Recover sources, layouts, users, and settings | A retained database backup or a stopped installation's data-directory copy |

Cloud database synchronization and backup controls require Pro. Cloud playlist/EPG publishing is available in the core workflow. See [Free vs Pro](../getting-started/free-vs-pro.md).

## Preserve the current desktop installation

Before updating, restoring, or making a large configuration change:

1. Wait for synchronization, output, and backup operations to finish.
2. Close IPTVBoss normally and allow its shutdown backup work to complete. Stop any noGUI or server process using the same database.
3. Locate the active [application data directory](../troubleshooting/application-files.md#application-folder-locations). Use the configured directory if the installation overrides the default.
4. Copy the data directory, including `db` and `backup`, into a dated folder outside the installation directory.
5. Confirm the copy contains the expected files and retain another copy on separate storage.

A data-directory copy preserves the installation files; it is not the same as the database backup file selected by the application's restore dialog. Follow [Restore a Database Backup](../troubleshooting/restore.md) when using that dialog.

## Configure database synchronization

1. Identify which installation contains the database you want to keep. This is the authoritative copy.
2. Open **Settings** → **IPTVBoss Settings** and review **Cloud & Backups**.
3. For Dropbox or Google Drive, complete [Cloud Provider Setup](cloud-providers.md). For XC Server, follow [desktop pairing](../server/gui-settings.md).
4. Review database synchronization and backup retention settings for the selected provider.
5. Save the settings and allow the backup operation to complete.
6. Check backup history or the configured provider for the resulting backup and review the logs for failures.

Keep a dated backup from before a major change. Synchronizing the latest database does not by itself preserve an older working configuration.

## Back up or update an XC Server

Use the instructions for the process that owns the server database:

- [Docker backup](../server/setup/docker.md#back-up-the-server), [update](../server/setup/docker.md#update-iptvboss), and [restore/rollback](../server/setup/docker-advanced.md#restore-a-backup).
- [Linux service](../server/setup/linux-service.md#back-up-and-upgrade).
- [macOS service](../server/setup/macos-service.md#back-up-and-upgrade).
- [Windows startup task](../server/setup/windows.md#back-up-and-upgrade-carefully).
- [Desktop Server Dashboard](../server/dashboard.md) for **Database Backup History**, **Download Backup**, and **Restore Backup** on a paired installation.

For recovery, choose the appropriate method in [Restore a Database Backup](../troubleshooting/restore.md). To reconnect an installation without replacing its database, use [XC paired-code recovery](../server/recovery.md#restore-an-xc-server-paired-code).
