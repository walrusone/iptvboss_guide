# Server Console: Link Restore

--8<-- "includes/xc-server-preview.md"

Use **Link Restore** to replace or initialize the active XC Server database from a supported backup link workflow.

![Server link restore](../../assets/images/server-console/link-restore.png)

In bootstrap mode, confirm which database will become authoritative before restoring. If the server is already paired, do not initialize a replacement database without owner approval. Keep the page open while restore progress is displayed, then verify the server status, paired devices, and logs before reconnecting clients.

## Restore from a backup link

1. Confirm the backup's date and which database it contains. Preserve the current installation using the [backup instructions](../../settings/backups.md#back-up-or-update-an-xc-server).
2. Open **Link Restore** in the Server Console. Complete any requested section-PIN unlock and obtain [editing access](../index.md#apply-changes-safely) when the console requires it.
3. Paste the Dropbox, Google Drive, or other supported HTTP(S) backup link into **Database backup link**.
4. Review the replacement warning, then select **Restore Database** and complete any confirmation.
5. Keep the page open while progress is displayed. Read the result, including cloud warnings or rollback information.
6. Verify the restored users, layouts, settings, paired devices, and [server logs](logs.md) before testing a player.

On an existing database, the server creates a verified local safety backup before replacement. Restored users, layouts, settings, and stream credentials may change; the server administrator and API keys remain server-local. After restoration, the server checks the restored cloud provider for a newer backup, so review the completion message before assuming the supplied link is the final database state.

For a new server, return to [First XC Server Connection](../first-connection.md). For a lost desktop connection or a server left in bootstrap mode, use [XC Server Recovery](../recovery.md).
