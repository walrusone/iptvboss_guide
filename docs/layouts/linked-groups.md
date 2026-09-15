# Linked Layout Groups

<span class="pro-badge">PRO</span> [See Free vs Pro](../getting-started/free-vs-pro.md).

A linked group reuses a group from another layout. Use it when several layouts should follow the same channel organization. Import an independent copy when each destination needs its own channel edits.

## Create a linked group

1. Open the destination layout in **Layout Editor**.
2. Select **Add Channels**, then **Switch to Layout Import**.
3. Select the originating layout and group.
4. Enable **Import as Linked Layout Group** and import the selected group.
5. Verify the group and its channels in the destination layout.

See [Channel Importer](importing-channels.md#import-from-another-layout) for selection and import controls.

## Maintain shared content

Make channel and group-content changes in the originating group. A link refers back to that group rather than storing an independent set of channels. Review every layout using it after changing the source group.

For a linked Custom Sports group, the originating group's sports settings control its filtering and presentation. Configure those settings in [Custom Sports Groups](../features/custom-sports.md).

To create an independently editable group, repeat the layout import with **Import as Linked Layout Group** disabled. Verify the copy before removing an obsolete link.

## Investigate a broken or empty link

1. Open [Layout Manager](layout-manager.md#read-layout-health) and inspect the affected layout's warning.
2. Confirm that the originating layout and group still exist.
3. Open that group and check its channels and their underlying sources.
4. Restore the missing configuration from a backup or import a new link to the intended group.
5. Review output in every affected destination layout.

If **Remove Empty Layout Groups After Source Sync** is enabled, a successful source sync can remove a linked group whose originating group is missing or empty. **Ignore Empty Group Health Check** does not prevent that cleanup. See [empty-group cleanup](layout-manager.md#remove-empty-groups-after-source-sync).

For XC Series playback that cannot identify a provider, see [server troubleshooting](../server/troubleshooting.md#series-playback-from-linked-groups).
