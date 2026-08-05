# Layout Output Settings

Layout output settings determine which files or links are generated and where they are published.

## Configure output for a layout

1. Open [Layout Manager](layout-manager.md).
2. Select a layout.
3. Select **Edit Layout**.
4. Review the output settings before enabling the layout.
5. Save the layout.

Common settings include:

| Setting | Purpose |
| --- | --- |
| **M3U Output Enabled** | Includes the layout in playlist output. |
| **EPG Output Enabled** | Includes guide output for the layout when available. |
| **XC Enabled** | Includes this layout in XC Server output when configured. It is not required when the XC Server is used only for M3U delivery. |
| **M3U Filename** | Sets the generated playlist filename. |
| **EPG Filename** | Sets the generated guide filename. |
| **Custom Output Folder** | Writes local output to a chosen folder. |
| **Cloud Provider Folder** | Selects the cloud-provider destination. |
| **Layout EPG - Days to Keep** | Controls the guide range retained for the layout. |
| **EPG Layout Override** | Applies layout-specific guide settings when configured. |
| **Upload Raw XML version of EPG** | Uploads an uncompressed XML guide. |
| **Upload Zipped XML (GZ) version of EPG** | Uploads a compressed XML guide. |

!!! note
    Exact labels can vary by release. Use the field shown in the current Layout Manager when a label differs.

## Choose local or cloud output

- Use **Custom Output Folder** for local files consumed by a player or another service on the same system.
- Use **Cloud Provider Folder** when the layout should publish to an authorized cloud provider.
- Use XC settings when this layout is intended for XC Server output. The XC Server itself can remain enabled for M3U delivery without enabling XC on any layout.

Do not enable multiple destinations until you understand which output is authoritative.

## Verify a layout

1. Confirm the layout is enabled.
2. Confirm M3U and EPG output settings match the intended player.
3. Generate output for the current layout.
4. Confirm the filenames and destination.
5. Test the resulting playlist and guide.
6. Use [Cloud Output Links](../setup/output-links.md) when the layout publishes cloud links.
