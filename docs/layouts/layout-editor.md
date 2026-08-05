# Editing a Layout

Use **Layout Editor** to control the groups, channels, names, logos, and EPG assignments in a layout.

![The Layout Editor](../assets/images/layout/layout-editor.png)

## Select a layout and group

1. Open **Layout Editor**.
2. Select the layout from the layout selector.
3. Select a group.
4. Review the channels in that group.

## Import channels with Channel Importer

Select **Add Channels** in the Layout Editor to open **Channel Importer**. The importer has two modes:

- **Source Import** adds groups and channels from an imported playlist or API source.
- **Layout Import** adds groups and channels that already exist in another layout.

Use **Switch to Layout Import** or **Switch to Source Import** at the bottom of the dialog to change modes. The selected mode determines whether the left-hand list is labeled **Source** or **Layout**.

### Import from a source

![Channel Importer: source import](../assets/images/layout/layout-editor-import-from-source.png)

In Source Import mode:

1. Select a source in the left panel.
2. Select a group in the middle panel to display its channels in the right panel. Use the **GROUPS** and **CHANNELS** checkboxes when you want to select items in bulk.
3. Select one or more groups or channels to import. Use the search field under a panel to find a matching item.
4. Choose **Import Source(s)**, **Import Group(s)**, or **Import Channel(s)**, depending on what is selected.

The **LIVE** dropdown filters the source content by the available content type. **Sort A to Z** controls the order shown in the group and channel lists. Sorting changes the display order in the dialog; it does not by itself reorder the destination layout.

The source importer also provides these options:

- **Keep Dialog Open after Import** leaves the importer open so you can repeat imports from the same source.
- **Add to Layout Group included Categories** adds imported source categories to the destination layout group when that grouping option is available. Use it when the source categories should become part of the selected layout group; leave it off when the imported categories should remain separate.
- **Don't Import Duplicate Channels** skips channels that are already present instead of adding another copy. Enable it for incremental imports and review the destination before disabling it.

### Import from another layout

![Channel Importer: layout import](../assets/images/layout/layout-editor-import-from-layout.png)

In Layout Import mode:

1. Select the source layout in the left panel.
2. Select a group in the middle panel to display its channels in the right panel.
3. Select the groups or channels to bring into the current layout.
4. Choose **Import Layout(s)**, **Import Group(s)**, or **Import Channel(s)**.

The layout importer is useful when a second layout needs some of the same channel organization as an existing layout. It copies selected content into the layout currently open in the editor; confirm the destination layout before importing.

### Linked Layout Groups

Enable **Import as Linked Layout Group** when the imported group should remain linked to its originating layout group rather than becoming an independent copy. This is useful when several layouts should reuse the same group structure and follow its source layout over time.

Leave the option disabled when the destination needs to be edited independently. A linked group should be treated as shared configuration: review the originating layout before making changes, and verify the resulting channels in every layout that uses the link.

### Avoid accidental imports

Before selecting an import button, confirm the current mode, the source or layout in the left panel, the selected groups and channels, and the destination layout. If the result is not expected, undo or remove the imported items before generating output.

## Edit a channel

1. Select a channel.
2. Change the channel name, number, logo, or other available field.
3. Assign or review its EPG source and channel identifier.
4. Save the change.

To edit the channel name directly, double-click the channel row. Press **Enter** to commit the edit or **Esc** to cancel it. Right-click a channel row for actions such as enabling or disabling channels, removing channels, moving selected channels to the top or bottom, and cutting, copying, or pasting channels.

Keep channel names consistent with the service you are editing. If you change a name only to improve matching, record the original name somewhere before saving.

## Organize groups and channels

Use the layout editor controls to:

- Create or rename groups.
- Move channels between groups.
- Reorder groups and channels.
- Remove items that should not appear in this output.

Click a group or channel row to select it. The group and channel lists support multiple selection; use Ctrl-click on Windows/Linux or Command-click on macOS to add individual rows to the selection. Drag a selected group or channel onto its new position to reorder it, or drag a channel onto a group to move it there. Review the destination before releasing the mouse because a drop changes the layout immediately.

Double-click a group name to edit it. Use the visible editor buttons for bulk actions such as sorting, renaming, merging, removing, and adding groups or channels.

Review the selected layout after each bulk change. A change made in one layout does not automatically change another layout.

## Import channels with Channel Importer

Select **Add Channels** in the Layout Editor to open **Channel Importer**. The importer has two modes:

- **Source Import** adds groups and channels from an imported playlist or API source.
- **Layout Import** adds groups and channels that already exist in another layout.

Use **Switch to Layout Import** or **Switch to Source Import** at the bottom of the dialog to change modes. The selected mode determines whether the left-hand list is labeled **Source** or **Layout**.

### Import from a source

![Channel Importer: source import](../assets/images/layout/layout-editor-import-from-source.png)

In Source Import mode:

1. Select a source in the left panel.
2. Select a group in the middle panel to display its channels in the right panel. Use the **GROUPS** and **CHANNELS** checkboxes when you want to select items in bulk.
3. Select one or more groups or channels to import. Use the search field under a panel to find a matching item.
4. Choose **Import Source(s)**, **Import Group(s)**, or **Import Channel(s)**, depending on what is selected.

The **LIVE** dropdown filters the source content by the available content type. **Sort A to Z** controls the order shown in the group and channel lists. Sorting changes the display order in the dialog; it does not by itself reorder the destination layout.

The source importer also provides these options:

- **Keep Dialog Open after Import** leaves the importer open so you can repeat imports from the same source.
- **Add to Layout Group included Categories** adds imported source categories to the destination layout group when that grouping option is available. Use it when the source categories should become part of the selected layout group; leave it off when the imported categories should remain separate.
- **Don't Import Duplicate Channels** skips channels that are already present instead of adding another copy. Enable it for incremental imports and review the destination before disabling it.

### Import from another layout

![Channel Importer: layout import](../assets/images/layout/layout-editor-import-from-layout.png)

In Layout Import mode:

1. Select the source layout in the left panel.
2. Select a group in the middle panel to display its channels in the right panel.
3. Select the groups or channels to bring into the current layout.
4. Choose **Import Layout(s)**, **Import Group(s)**, or **Import Channel(s)**.

The layout importer is useful when a second layout needs some of the same channel organization as an existing layout. It copies selected content into the layout currently open in the editor; confirm the destination layout before importing.

### Linked Layout Groups

Enable **Import as Linked Layout Group** when the imported group should remain linked to its originating layout group rather than becoming an independent copy. This is useful when several layouts should reuse the same group structure and follow its source layout over time.

Leave the option disabled when the destination needs to be edited independently. A linked group should be treated as shared configuration: review the originating layout before making changes, and verify the resulting channels in every layout that uses the link.

### Avoid accidental imports

Before selecting an import button, confirm the current mode, the source or layout in the left panel, the selected groups and channels, and the destination layout. If the result is not expected, undo or remove the imported items before generating output.

## Check the result

Before generating output, confirm that:

1. The layout is enabled when it should be.
2. Required groups and channels are present.
3. EPG mappings are assigned to the intended channels.
4. Logos and names are correct.
5. The layout is saved.

!!! warning
    Do not use a destructive bulk action until you have selected the intended layout, group, or channel set.
