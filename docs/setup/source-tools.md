# Source and EPG Tools

The **Sources** menu contains maintenance tools for imported channels, categories, EPG data, users, tags, and Advanced EPG Dummies.

![Sources menu](../assets/images/sources-menu.png)

## Sources Manager

Use **Sources** → **Sources Manager** to review imported playlist and EPG sources, start synchronization, and inspect source status.

![Sources Manager](../assets/images/sources/sources-manager.png)

Use **Sync All Sources** when all configured playlist sources should be refreshed. For a controlled test, synchronize one selected source from **Sources Manager** instead.

## Remove provider prefixes

**Prefix Auto-Removal** can help normalize provider channel names before mapping and sorting.

1. Open **Sources** → **Prefix Auto-Removal**.
2. Review the detected or configured prefixes.
3. Select only prefixes that should be removed from the imported names.
4. Apply the change.
5. Recheck channel mappings and output names.

![Prefix Auto-Removal](../assets/images/sources/prefix-auto-removal.png)

!!! warning
    Name cleanup can affect matching, AED regexes, and output. Record the original naming pattern before applying a broad change.

## Manage categories

Use **Manage Categories** in the source dialog before saving a new playlist. The same view can be opened later when category membership or category names have changed at the provider.

The dialog shows separate lists for **Live Categories**, **VOD Categories**, and **Series Categories**. The count below each list reports how many categories are shown, how many exist in total, and how many are stale. Use the filter field above a list to narrow the visible categories.

1. Open **Manage Categories** from the source workflow.
2. Select **Refresh Categories** in the category view.
3. Wait for the provider's current categories to load.
4. Click a category to select it. Use Ctrl-click on Windows/Linux or Command-click on macOS to select multiple categories.
5. Right-click the selected categories to use the available actions:
   - **Select All** selects every category in that list.
   - **Enable Selected** includes the selected categories in the source.
   - **Disable Selected** excludes the selected categories from the source.
   - **Clear Selected Stale** removes selected categories that are no longer current, when stale categories are present.
6. Repeat the selection and enable/disable actions independently in the Live, VOD, and Series lists.
7. Close the category view to keep the selection, then save the source dialog.
8. Confirm that the affected layout groups still contain the intended channels.

![Refresh categories](../assets/images/epg/manage-refresh-categories.png)

## Manage channels and categories

The layout tools can create or manage channels and categories when the imported provider data does not match the desired organization.

- Use **New Channel Manager** to create or maintain channel entries.
- Use **New Category Manager** to create or maintain category entries.

![New Channel Manager](../assets/images/layout/new-channel-manager.png)

![New Category Manager](../assets/images/layout/new-category-manager.png)

Review the resulting layout and output after making manual entries. Do not create duplicates when an existing source channel can be mapped instead.

## Manage new tags

Use **Sources** → **Manage New Tags** to review tags discovered during source or EPG processing.

![Manage New Tags](../assets/images/sources/manage-new-tags.png)

Confirm the tag behavior before applying it globally. Tags can affect grouping, filtering, or output depending on the configuration.

## Manage users

Use **Sources** → **Manage Users** to create users, enable or disable them, assign enabled layouts, and configure source credentials. See [User Management](../layouts/users.md) for the provider and multi-user workflow.
