# Server Console: Link Restore

--8<-- "includes/xc-server-preview.md"

Use **Link Restore** to replace or initialize the active XC Server database from a supported backup link workflow.

![Server link restore](../../assets/images/server-console/link-restore.png)

In bootstrap mode, confirm which database will become authoritative before restoring. If the server is already paired, do not initialize a replacement database without owner approval. Keep the page open while restore progress is displayed, then verify the server status, paired devices, and logs before reconnecting clients.
