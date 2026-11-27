# XC Server Troubleshooting

For interrupted background syncs, cancellation timeouts, or a NoGUI status that needs attention, see [NoGUI status and cancellation](../settings/automation.md#nogui-status-and-cancellation).

--8<-- "includes/xc-server-pro.md"

## Series playback from linked groups

For Series playback from a layout containing linked series groups, XC Server can resolve the upstream provider even when no provider was previously remembered. It follows the enabled linked groups and uses the provider when exactly one XC source is available. Playback is still rejected when no provider or multiple providers can be identified; confirm that the user has an enabled credential for the resolved source.

See [Linked Layout Groups](../layouts/linked-groups.md) to check the originating layout and group.

## Server did not reload after a client update

<span class="pro-badge">PRO</span> This troubleshooting workflow applies to XC Server.

For local databases or Dropbox/Google Drive database synchronization, the automatic reload request is sent when a paired IPTVBoss client closes after completing its database backup workflow. It is not sent after every edit. When XC Server is the database synchronization provider, the server applies the uploaded backup directly; no separate reload request is expected. For that workflow, see [XC shutdown or startup needs attention](#xc-shutdown-or-startup-needs-attention).

Check the following:

1. Confirm the desktop installation still appears under the XC Server's [Paired Devices](console/paired-devices.md).
2. Confirm XC is enabled in the client's saved Server Settings and that the configured server address is reachable from that computer.
3. If database cloud synchronization is enabled, confirm the shutdown cloud backup completed successfully. IPTVBoss intentionally skips the reload request after a failed or incomplete cloud backup.
4. Check whether the server was already updating, processing an administration or restore operation, or blocked by a database synchronization lock.
5. Review the client log for the reload notification and HTTP response, then review the [XC Server logs](console/logs.md) for the queued or rejected request.
6. If the client was revoked, generate a new one-time pairing code and pair it again. Do not reuse an expired pairing code or manually share a reload credential.

See [Automatic server reloads from this client](gui-settings.md#automatic-server-reloads-from-this-client) for the expected behavior.

## XC shutdown or startup needs attention

**Shutdown needs attention** means the desktop could not finish its shutdown work. Read the failure detail and record the displayed local backup path, then:

1. Check the server connection and review the desktop and XC Server logs. Backup phase timings can help distinguish backup creation, upload, server processing, and cleanup delays.
2. Choose **Retry**. If the server already accepted an upload, IPTVBoss checks that operation rather than uploading again. If the upload outcome is uncertain, it checks server revision history and keeps the operation unresolved when success cannot be confirmed.
3. If you need to close, choose **Exit with Local Backup** when enabled. This requires a verified local backup and does not mean that publication succeeded.

At the next startup, pending publication and restore work must be resolved before normal editing and automatic source sync resume. If **XC database reconciliation required** appears:

- **Retry Connection** attempts to resolve the pending work and restore the server database. Use it after restoring connectivity or resolving the reported server issue.
- **Exit** leaves the recovery unresolved for a later launch; follow any shutdown prompt that appears.
- **Unlink Server** saves a local safety backup, disables synchronization, and allows editing the local copy. Confirm this only when you intend to abandon automatic reconciliation: the local copy may lack server-side changes. The server database is not modified. Unlinking cannot proceed if the safety backup fails.

See [Shutdown publication and next startup](gui-settings.md#shutdown-publication-and-next-startup) for the normal workflow.

## Server database is preserved at startup

In **3.12.30 and later**, when the existing server database selects **XC Server** as its database synchronization provider, startup opens that database without replacing it with the desktop database. First startup and legacy synchronization modes retain the desktop-copy workflow.

If startup reports that it cannot read the server database's synchronization ownership, it stops without copying the desktop database or attempting startup backup restoration. Review the [server logs](console/logs.md) for database access or corruption errors, preserve the existing database and backups, and resolve the reported problem before restarting. Use [XC Server Recovery](recovery.md) when a deliberate restore is needed.

## A server sync was skipped or deferred

In **3.12.30 and later**, logs identify rejected manual and scheduled launches with `NoGUI launch rejected` and a `blocker` value. Scheduled runs also record the reason in their history.

If the message says **Sync deferred until user changes are published**, check the configured backup destination and the accompanying publication error. The server must confirm publication of pending user changes before starting its internal NoGUI run. Resolve the error and retry; starting another run does not bypass publication.

Other blockers include active editing ownership, a database restore, queued administration work, or another background sync. Let that operation finish or resolve its reported recovery state before retrying. See [Before a server sync starts](console/settings.md#before-a-server-sync-starts) for the normal sequence.

## XC output is rebuilding or serves an older response

XC Server now prepares and publishes complete response variants, then keeps the previous valid generation available while a replacement is built. A request can temporarily use direct database generation when its cached variant is unavailable or when the database is changing.

Do not delete XC cache files while the server is running. Check the client and [XC Server logs](console/logs.md) for database-transition, cache-build, direct-fallback, or out-of-memory messages. If the problem continues, restart the XC Server after creating a database backup and include the sanitized logs with the application version when requesting support.
