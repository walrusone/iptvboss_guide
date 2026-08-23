# Sources Manager

**Sources Manager** is the main screen for reviewing, editing, and synchronizing playlist sources and EPG sources. Open it from **Sources** → **Sources Manager**.

![Sources Manager](../assets/images/sources/sources-manager.png)

The redesigned Sources Manager adapts to the available window size. Its two independent sections—**IPTV Sources** and **EPG Sources**—can each be expanded or collapsed from the section header. IPTVBoss remembers your choices between uses, which is useful when working in a smaller window.

The screen has two independent areas: playlist sources at the top and EPG sources below. Select an item in a list to populate its details and actions. Double-click a playlist or EPG source to open its editor.

## Playlist sources

The playlist section shows the configured M3U, XC API, and custom sources. The details panel can include the source name, expiry, connection limit, time zone, channel totals, category totals, user status, and the last attempt, successful synchronization, and output times.

### Playlist toolbar

- **Add M3U source** creates a standard M3U or M3U URL source.
- **Add XC source** creates an Xtream Codes API source.
- **Add custom source** creates a source using the custom-source workflow.
- **Remove source** deletes the selected source after confirmation. Review dependent layouts and output before removing it.

### Selected-source actions

- **Sync** downloads and processes the selected playlist source now. This can add new categories/channels, update existing data, and process deleted channels according to the source and category settings.
- **Source Settings** opens the editor for the selected source. Double-clicking the source has the same editing purpose.
- **View Added** opens the channels added during the source's recent processing so they can be reviewed.
- **View Removed** opens the channels marked or processed as removed.
- **Add EPG** creates an EPG source from information available in the selected playlist/source data when that workflow is supported.

Use **Sync** on one selected source when testing a change. Use the global **Sources** → **Sync All Sources** command when all configured playlist sources should be refreshed.

## EPG sources

The EPG section lists configured EPG feeds, including the built-in dummy source when present. The details panel shows the source name, channel count, synchronization/output times, preferred language, and whether the source is available for mapping or output.

### EPG toolbar

- **Add EPG source** creates a new XMLTV or other supported EPG source.
- **Remove EPG source** removes the selected EPG source after confirmation. Check current channel mappings first.
- **Sort A to Z** sorts the EPG source list alphabetically and saves the new order. When multiple sources are selected, the selected range is sorted.

### Selected-EPG actions

- **Sync** downloads and processes the selected EPG source now.
- **EPG Settings** edits the selected EPG source. The built-in dummy source opens its dummy-source editor instead.
- **Map EPG** opens the EPG assignment workflow for the selected source. Mapping is what makes its programme data available to layout channels.
- **Output Logo Template** opens logo-template settings for the selected EPG source.
- **Output All Logos** opens logo-template settings for all EPG sources.

The EPG options below the details include **Include in Search**, which makes the source available to EPG search/mapping workflows, and **Sort by EPG ID**, which changes how its EPG channels are ordered. **Preferred Language** controls the language selected from multilingual EPG data when the source provides that choice.

## Synchronization workflow

1. Select the source to inspect.
2. Review its last attempt and last successful synchronization time.
3. Select **Sync** and wait for the progress operation to finish.
4. Review added or removed channels when needed.
5. Check affected layouts before generating output.

Synchronization can update source metadata and layout content. Avoid starting another source operation while the current progress dialog is running.

!!! note
    Sources Manager is for source maintenance. Use the [New Channel Manager](../layouts/new-channel-manager.md) and [New Category Manager](../layouts/new-category-manager.md) to control how future source changes are placed into layouts.
