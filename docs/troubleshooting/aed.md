# AED Troubleshooting

For a missing event, first [check the source, assignment, and sports data](common-problems.md#a-sports-channel-has-no-current-event). For an interrupted refresh, use [Refresh Pending](../features/aed-maintenance.md#refresh-aed-results).

## A usable channel name still produces No Event

In **3.12.30 and later**, [name priority](../features/aed-maintenance.md#name-priority-and-no-event-results) determines lookup order rather than excluding the other name. Check both the original provider name and the edited name. An explicit provider **NO EVENT** marker still blocks normal matching, while supported custom sports TXT overrides remain available.

Refresh the assigned AED channels after updating. Previously incorrect hard no-event results are re-evaluated automatically; no database restore or cosmetic name change is needed to trigger recovery. If a usable name still finds no event, check the sports data, match window, and fallback configuration. Verbose diagnostics identify the name sources and rules evaluated; include sanitized examples when requesting support.

## AED database integrity repair

At startup, IPTVBoss verifies the event reference used by AED channel assignments. If an older database migration left that reference pointing to an invalid table-copy target, IPTVBoss repairs it, clears assignments whose events no longer exist, and queues those assignments for a later AED refresh. The repair is recorded in the log. If the repair cannot be completed, AED persistence is disabled until the database is reopened; keep the database backup and review the logged failure before retrying.


Collect [Logs and Diagnostics](logs.md) if the repair fails.
