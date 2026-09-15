# Choose a Scheduling Method

First confirm that your sources synchronize and your layout generates output manually. Then choose the scheduler for the installation that will perform those operations.

| Installation | Method | Instructions |
| --- | --- | --- |
| Desktop with Pro | Create schedules in IPTVBoss and apply the system schedule on supported installations | [Sync Schedule](scheduling.md) |
| Desktop using an operating-system task | Run noGUI synchronization with Windows Task Scheduler or cron | [External noGUI Scheduling](automation.md) |
| Continuously running XC Server | Configure the server's own schedules | [Server Console: Current Schedules](../server/console/settings.md#current-schedules) |

Use one scheduler for a given job and database. Review existing operating-system tasks before applying a new schedule to avoid duplicate runs. The desktop application must be closed before a standalone noGUI run uses its database.

## Related tasks

- [Database Sync and Backups](backups.md) explains how to protect and synchronize your configuration.
- [Email Notifications](email.md) configures provider-credential expiry notifications.
- [Automatic NoGUI user checks](automation.md#automatic-nogui-user-checks) explains which provider credentials are refreshed during noGUI runs.
- [Logs and Diagnostics](../troubleshooting/logs.md) helps investigate a failed run.

To publish playlist and guide files to Dropbox or Google Drive, use [Cloud Provider Setup](cloud-providers.md) and [Cloud Output Links](../setup/output-links.md).
