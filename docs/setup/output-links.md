# Cloud Output Links

When cloud output is configured, IPTVBoss can display the generated M3U and EPG links for use in a player or downstream service.

## Generate cloud output

1. Confirm that the intended [layout](../layouts/layout-manager.md) is enabled.
2. Confirm that cloud output is configured for the layout.
3. Open **Output**.
4. Select **All Layouts M3Us & EPGs** or the current-layout output action required by the test.
5. Wait for the output operation to finish.
6. Select **Output** → **View Cloud Links**. See [Layout Output Settings](../layouts/output-settings.md) if the destination is not configured.
7. Copy only the link intended for the selected user or player.

!!! warning
    Cloud links can provide access to provider content. Treat them as credentials and never share them publicly.

## Verify a link

1. Confirm that the link belongs to the intended layout or user.
2. Open it only in a trusted player or controlled test tool.
3. Confirm that the playlist loads.
4. Confirm that the EPG link returns guide data when one was generated.
5. Check the layout output if channels or guide data are missing.

If a link is stale, regenerate the relevant output and check the cloud authorization in **IPTVBoss Settings**.

Cloud-link lifetime and expiration behavior can vary. Do not document or rely on a fixed expiration period.
