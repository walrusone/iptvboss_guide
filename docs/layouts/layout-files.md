# Import and Export Layout Files

<span class="pro-badge">PRO</span> [See Free vs Pro](../getting-started/free-vs-pro.md).

Use a layout export to transfer selected layouts and the source/EPG definitions needed by them. Use a [database backup](../settings/backups.md) when you need to preserve the whole installation.

| Task | Action |
| --- | --- |
| Transfer layout configuration between installations | **Export Layout(s)** / **Import Layout(s)** |
| Copy channels or groups between layouts in this database | [Channel Importer](importing-channels.md#import-from-another-layout) |
| Produce a readable channel inventory | **Export Channel List** |
| Produce a playlist for a player | [Creating Output](../setup/output.md) |

## Export layouts

1. Open **Layout** → **Layout Manager**.
2. Select the layouts to export. Use Ctrl-click on Windows/Linux or Command-click on macOS for multiple selections.
3. Select **Export Layout(s)**.
4. Enter a filename and select **OK**.
5. If that export already exists, confirm replacement only when it is intended.
6. Wait for completion. Find the resulting `NAME.mv.db` in `Layout_Exports` under the active [application data directory](../troubleshooting/application-files.md).

Keep exports private. Source definitions and URLs can contain sensitive information; do not assume an export is suitable for public sharing.

## Import layouts

1. [Back up the destination installation](../settings/backups.md) before importing.
2. Open **Layout Manager** and select **Import Layout(s)**.
3. Choose the exported `.mv.db` file.
4. If an imported source cannot be matched automatically, choose the corresponding existing source or **Import as New Source**. Supply provider credentials when prompted for a new source.
5. Wait for the import, database reload, and follow-up synchronization to finish.
6. Review the imported layouts, source/category assignments, EPG mappings, and output destinations.
7. Generate output for one imported layout and verify it in a player before using an all-layout output action.

Align an imported source with an existing source only when they represent the same provider content. This choice determines which source channels the imported layout uses.

## Export a channel inventory

Select a layout in **Layout Manager**, then select **Export Channel List**. IPTVBoss writes a CSV named after the layout in the application data directory. It lists group and channel names for enabled groups and channels.

The CSV is an inventory, not the layout database file accepted by **Import Layout(s)**. For a player playlist, generate M3U output.
