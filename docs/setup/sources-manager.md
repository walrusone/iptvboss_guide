# Sources Manager

**Sources Manager** is the main screen for reviewing, editing, and synchronizing playlist sources and EPG sources. Open it from **Sources** → **Sources Manager**.

![The 3.11.94 IPTV Sources Manager](../3.11.94_Screens/Sources_Manager_IPTV.png)

The Sources Manager now separates playlist and guide maintenance into two tabs: **IPTV Sources** and **EPG Sources**. Each tab uses a split view with the source list on the left and the selected source’s health, details, actions, and history on the right. The detail panels adapt to the available window size.

Select an item in a list to populate its details and enable actions for that source. Each list row shows a health indicator, source type, health label, and last successful sync. Double-click a playlist or EPG source to open its settings.

## Understand source health

The selected source header and its list row can show:

- **Healthy** — the last sync completed successfully.
- **Needs attention** — the source has not been synced, a sync was skipped or is running, a playlist category needs attention, or an XC account is expired or expiring within 30 days.
- **Sync failed** — the last sync did not complete successfully.
- **Built-in** — the selected EPG is the built-in dummy source.

The health badge summarizes the source state; it does not replace reviewing the last attempt, last successful sync, and last output in **Sync history**.

## Playlist sources

The playlist section shows the configured M3U, Xtream Codes, and custom sources. The details panel can include the source name, expiry, connection limit, time zone, channel totals, category totals, user status, and the last attempt, successful synchronization, and output times.

### Playlist toolbar

- ![](../assets/icons/ui/add.svg){ .ui-icon } **Add Source** opens a menu. Choose **Add M3U Source**, **Add XC Source**, or **Add Custom Source** from that menu.
- ![](../assets/icons/ui/remove.svg){ .ui-icon } **Remove Source(s)** deletes the selected sources after confirmation. Review dependent layouts and output before removing them.

### Selected-source actions

- ![](../assets/icons/ui/refresh.svg){ .ui-icon } **Sync** downloads and processes the selected playlist source now. This can add new categories/channels, update existing data, and process deleted channels according to the source and category settings.
- ![](../assets/icons/ui/settings.svg){ .ui-icon } **Source Settings** opens the editor for the selected source. Double-clicking the source does the same thing.
- ![](../assets/icons/ui/menu.svg){ .ui-icon } **More** opens a menu containing **View Added**, **View Removed**, and **Add EPG**.

Use **View Added** or **View Removed** to review recent provider changes. **Add EPG** creates an EPG source from information available in the selected playlist when that workflow is supported.

Use ![](../assets/icons/ui/refresh.svg){ .ui-icon } **Sync** on one selected source when testing a change. Use the global **Sources** → **Sync All Sources** command when all configured playlist sources should be refreshed.

### Playlist detail sections

The selected playlist source is organized into collapsible sections:

- **Inventory** shows total, active, added, and removed channels, followed by LIVE, VOD, and SERIES category counts.
- **Access & expiry** shows XC expiry, maximum connections, timezone, and the source-user summary when those values apply. User counts can be selected to open the relevant user-management view.
- **Sync history** shows the last attempt, last successful sync, and last output.

The **Inventory**, **Access & expiry**, and **Sync history** expansion choices are remembered. Fields that do not apply to an M3U or custom source are hidden rather than treated as zero.

## EPG sources

The EPG section lists configured EPG feeds, including the built-in dummy source when present. The details panel shows the source name, channel count, synchronization/output times, preferred language, and whether the source is available for mapping or output.

![The 3.11.94 EPG Sources Manager](../3.11.94_Screens/Sources_Manager_EPG.png)

### EPG toolbar

- ![](../assets/icons/ui/add.svg){ .ui-icon } **Add EPG** creates a new XMLTV or other supported EPG source.
- ![](../assets/icons/ui/remove.svg){ .ui-icon } **Remove EPG(s)** removes selected EPG sources after confirmation. Check current channel mappings first.
- ![](../assets/icons/ui/sort_az.svg){ .ui-icon } **Sort A to Z** sorts the EPG source list alphabetically and saves the new order. When multiple sources are selected, the selected range is sorted.

### Selected-EPG actions

- ![](../assets/icons/ui/refresh.svg){ .ui-icon } **Sync** downloads and processes the selected EPG source now.
- ![](../assets/icons/ui/settings.svg){ .ui-icon } **EPG Settings** edits the selected EPG source. The built-in dummy source opens its dummy-source editor instead.
- ![](../assets/icons/ui/linked.png){ .ui-icon } **Map EPG to Source** opens the workflow that assigns the selected EPG to playlist sources.
- ![](../assets/icons/ui/menu.svg){ .ui-icon } **More** opens **Output Logo Template** for the selected EPG source and **Output All Logo Templates** for all EPG sources.

The EPG details are organized into **Inventory**, **Output & matching**, and **Sync history**. **Inventory** shows the total EPG channel count. **Output & matching** contains **Preferred Language**, **Included EPG in Search**, **Sort by Epg-ID (When unchecked, sort is by Display Name)**, mapping, and logo-template actions. **Preferred Language** controls the language selected from multilingual EPG data when the source provides that choice.

The **Inventory**, **Output & matching**, and **Sync history** expansion choices are remembered. The built-in dummy source is shown as **Built-in** and cannot be synced manually. Custom EPG sources can also display a message when their sync window or daily sync limit prevents a manual sync.

## Synchronization workflow

1. Select the source to inspect.
2. Review its last attempt and last successful synchronization time.
3. Select ![](../assets/icons/ui/refresh.svg){ .ui-icon } **Sync** and wait for the progress operation to finish.
4. For a playlist source, use ![](../assets/icons/ui/menu.svg){ .ui-icon } **More** to review added or removed channels when needed.
5. Check affected layouts before generating output.

Synchronization can update source metadata and layout content. Avoid starting another source operation while the current progress dialog is running.

!!! note
    Sources Manager is for source maintenance. Use the [New Channel Manager](../layouts/new-channel-manager.md) and [New Category Manager](../layouts/new-category-manager.md) to control how future source changes are placed into layouts.
