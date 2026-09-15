# AED Troubleshooting

For a missing event, first [check the source, assignment, and sports data](common-problems.md#a-sports-channel-has-no-current-event). For an interrupted refresh, use [Refresh Pending](../features/aed-maintenance.md#refresh-aed-results).

## AED database integrity repair

At startup, IPTVBoss verifies the event reference used by AED channel assignments. If an older database migration left that reference pointing to an invalid table-copy target, IPTVBoss repairs it, clears assignments whose events no longer exist, and queues those assignments for a later AED refresh. The repair is recorded in the log. If the repair cannot be completed, AED persistence is disabled until the database is reopened; keep the database backup and review the logged failure before retrying.


Collect [Logs and Diagnostics](logs.md) if the repair fails.
