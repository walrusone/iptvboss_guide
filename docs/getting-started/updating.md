# Updating IPTVBoss

Update IPTVBoss while protecting the database, settings, layouts, and source configuration already on the computer.

!!! note "Startup update check"
    Packaged desktop installations check for a newer release before loading application data. The prompt shows the installed and available versions. A failed or timed-out update check allows normal startup to continue.

For a server, use the [platform-specific backup and update procedures](../settings/backups.md#back-up-or-update-an-xc-server).

## Update from 3.11.16

Read [Update from 3.11.16](update-from-3.11.16.md) before upgrading an existing installation. It covers the new XC Server administrator username (`admin` with your existing password) and the settings for reverse proxy, direct LAN HTTP, and direct HTTPS connections.

## Use the startup update prompt

When an update is available:

- Choose **Update now** when offered to hand installation to the native updater and restart before loading your data.
- Choose **Later** to continue using the installed version.
- If **Open download page** appears instead, download and install the update manually, then relaunch with your usual shortcut or command. This applies when the native updater is unavailable, including launches with command-line arguments or software rendering.

On Linux, update a Debian/Ubuntu package through your package manager. For a tarball installation, stop IPTVBoss and replace the installation using the download for the matching architecture and Ubuntu variant. Preserve the application data directory.

If local XC Server or sync work is active, or its status cannot be verified, the prompt reports **Update available — installation deferred**. Choose **Continue to Boss** or **Exit**, stop the local work safely, and reopen the desktop to update. IPTVBoss does not stop that work for you.

### Recover an incomplete update

While an update is pending, new background launches are deferred. If startup reports **Previous update has not completed**, let any running installer finish. If the installer has failed and closed, choose **The updater has closed — recover startup**. Background jobs remain paused until the update succeeds or startup is recovered.

If the native updater cannot start, use the offered download page to install manually. See [If the update fails](#if-the-update-fails) if the installation itself fails.

## Before updating

1. Finish any source synchronization and output operation, or select **Cancel** for an in-progress source sync and wait for its progress view to close.
2. Confirm that no second IPTVBoss process is using the database.
3. [Create or confirm a recent backup](../settings/backups.md).
4. Record the current IPTVBoss version and operating system.
5. Download the new installer only from the [official IPTVBoss download page](https://walrusone.github.io/iptvboss-release/download.html). Use the [GitHub Releases page](https://github.com/walrusone/iptvboss-release/releases/latest) when you need direct assets or release notes.

!!! warning
    Do not remove the existing application data directory as part of a normal update. That directory contains configuration and database files.

## Install the update

1. Close IPTVBoss.
2. Install the package for your operating system.
3. Start IPTVBoss.
4. Wait for the database migration or startup process to finish.
5. Confirm that your layouts, sources, and settings are present.

Do not interrupt a database migration. If startup fails, stop and collect the logs before trying a reset or restore.

## Confirm the update

Check the version shown by the application, then test a low-risk workflow:

1. Open [Sources Manager](../setup/sources-manager.md).
2. Confirm that a source is present and review its health and last successful sync without synchronizing it yet.
3. Open [Layout Manager](../layouts/layout-manager.md), confirm that your layouts are present, and review the selected layout’s health dashboard.
4. Review [Application Settings](../settings/application.md), including the Layout Manager attention checks when the release adds them.
5. Generate a test output only after the database, source health, layout inventory, and settings look correct.

## If the update fails

Do not delete the database. Save the logs, note the version you installed, then ask in the [IPTVBoss Discord](https://discord.gg/s3kpjP8EgR) or open a [support ticket through the member portal](https://members.bosstees.net/) if the issue is account-specific or private.

!!! note
    The release page and installer names may change as new IPTVBoss releases are published.
