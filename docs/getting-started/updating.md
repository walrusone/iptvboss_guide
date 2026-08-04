# Updating IPTVBoss

Update IPTVBoss while protecting the database, settings, layouts, and source configuration already on the computer.

!!! note "Automatic updates"
    Windows and macOS installations should normally update automatically when a new release is available. Use this page when an automatic update does not complete, when you need to verify the installed version, or when updating a Linux package manually.

## Before updating

1. Finish or cancel any source synchronization and output operation.
2. Confirm that no second IPTVBoss process is using the database.
3. Create or confirm a recent database backup.
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

1. Open [Source and EPG Tools](../setup/source-tools.md) and select **Sources Manager**.
2. Confirm that a source is present without synchronizing it yet.
3. Open [Layout Manager](../layouts/layout-manager.md) and confirm that your layouts are present.
4. Review [Application Settings](../settings/application.md).
5. Generate a test output only after the database and settings look correct.

## If the update fails

Do not delete the database. Save the logs, note the version you installed, and open a support ticket through the [IPTVBoss member portal](https://members.bosstees.net/).

!!! note
    The release page and installer names may change as new IPTVBoss releases are published.
