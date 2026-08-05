# Source and EPG Tools

The **Sources** menu contains maintenance tools for imported channels, categories, EPG data, users, tags, and Advanced EPG Dummies.

![Sources menu](../assets/images/sources-menu.png)

## Sources Manager

Use **Sources** → **Sources Manager** to review imported playlist and EPG sources, start synchronization, and inspect source status.

![Sources Manager](../assets/images/sources/sources-manager.png)

Use **Sync All Sources** when all configured playlist sources should be refreshed. For a controlled test, synchronize one selected source from **Sources Manager** instead.

## Remove provider prefixes

💲 [Pro feature — see Free vs Pro](../getting-started/free-vs-pro.md).

**Prefix Auto-Removal** can help normalize provider channel names before mapping and sorting.

1. Open **Sources** → **Prefix Auto-Removal**.
2. Review the detected or configured prefixes.
3. Select only prefixes that should be removed from the imported names.
4. Apply the change.
5. Recheck channel mappings and output names.

![Prefix Auto-Removal](../assets/images/sources/prefix-auto-removal.png)

!!! warning
    Name cleanup can affect matching, AED regexes, and output. Record the original naming pattern before applying a broad change.

## Playlist category maintenance

Category selection belongs to the playlist source workflow. See [Manage Categories in Adding a Playlist](playlists.md#manage-categories) when adding or editing a source. After changing categories, confirm that the affected layout groups still contain the intended channels.

## Manage channels and categories

The layout tools can create or manage channels and categories when the imported provider data does not match the desired organization.

- Use **New Channel Manager** to create or maintain channel entries.
- Use **New Category Manager** to create or maintain category entries.

![New Channel Manager](../assets/images/layout/new-channel-manager.png)

![New Category Manager](../assets/images/layout/new-category-manager.png)

Review the resulting layout and output after making manual entries. Do not create duplicates when an existing source channel can be mapped instead.

## 💲 Manage new tags

💲 [Pro feature — see Free vs Pro](../getting-started/free-vs-pro.md).

Use **Sources** → **Manage New Tags** to review tags discovered during source or EPG processing.

![Manage New Tags](../assets/images/sources/manage-new-tags.png)

Confirm the tag behavior before applying it globally. Tags can affect grouping, filtering, or output depending on the configuration.

## Manage users

Use **Sources** → **Manage Users** to create users, enable or disable them, assign enabled layouts, and configure source credentials. See [User Management](../layouts/users.md) for the provider and multi-user workflow.
