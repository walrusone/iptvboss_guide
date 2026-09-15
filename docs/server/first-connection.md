# First XC Server Connection

--8<-- "includes/xc-server-preview.md"

Use this checklist to take an installed XC Server through database setup and a first player test.

## 1. Install and open the console

Choose an [installation method](setup/index.md) and complete its health checks. Open the Server Console at the address provided by that installation, ending in `/boss.php`.

Follow [Login and Setup](console/login.md) to create the first administrator account and section PIN. Review [Console Security](console/security.md).

## 2. Initialize the database

If the server is in bootstrap mode, choose one path:

- [Pair an IPTVBoss Pro desktop installation](gui-settings.md#bootstrap-an-empty-xc-server-with-gui-pairing) to publish the database currently open on that desktop.
- Use [Link Restore](console/link-restore.md) with a supported database backup link.

Confirm that the selected database is the copy you want the server to use. Wait for initialization to finish before editing users or configuring schedules.

## 3. Connect desktop installations

For additional installations, generate a code under [Paired Devices](console/paired-devices.md) and enter it in the desktop's **Server Settings**. Review [XC Cloud Database](gui-settings.md#xc-cloud-database) and the [Desktop Server Dashboard](dashboard.md) to confirm pairing and synchronization status.

Pairing connects an IPTVBoss installation for administration and database synchronization. IPTV players use the output links or XC user credentials configured in the next step.

## 4. Configure delivery and test a player

1. Confirm the database has the intended sources, channel mappings, and enabled layout.
2. Review [Layout Output Settings](../layouts/output-settings.md). Enable **XC Enabled** for a layout that will provide XC login access.
3. Configure [users and provider credentials](console/users.md), using the console's [Begin Editing / End Editing workflow](index.md#apply-changes-safely).
4. Complete [Connect a Player](../setup/connect-player.md) for M3U/XMLTV or XC login.
5. Review [User Activity](console/activity.md) and [Logs](console/logs.md) after the test.

## 5. Schedule and back up

Once the first connection works, configure [server schedules](console/settings.md#current-schedules) and [backups](../settings/backups.md#back-up-or-update-an-xc-server). Review [automatic reloads from a paired desktop](gui-settings.md#automatic-server-reloads-from-this-client) so you know when desktop changes reach the server.
