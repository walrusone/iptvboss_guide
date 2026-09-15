# Custom Sources

A custom source holds Live channels you add manually. Use it when you have individual stream addresses rather than an M3U playlist or an Xtream Codes provider connection.

This is separate from **Custom** in the Add EPG dialog, which adds programme data. A custom playlist source still needs channels imported into a layout and EPG mappings assigned where required.

## Create a source and category

1. Open **Sources** → **Sources Manager** → **IPTV Sources**.
2. Select **Add Source** → **Add Custom Source**.
3. Enter a unique source name.
4. Select **Add Category**, name the category, and confirm it.
5. Select the category before adding channels.

The source editor also offers prefix/suffix and source-color settings. Start with the source name and channels; adjust presentation after verifying the stream.

## Add or edit channels

1. Select a category and choose **Add Channel**.
2. Enter the channel name and stream link. Enter a logo address when one is available.
3. Select **OK** to save the channel.
4. Repeat for the remaining channels.
5. Select **OK** in the source editor when finished.

To edit a channel later, open the source settings, select its category, and double-click the channel. Review the stream link carefully: it identifies the source channel, so changing it can affect existing layout references. Category and channel actions can save changes while the editor is open; do not assume closing the outer dialog cancels all earlier actions.

Right-click categories to toggle their inclusion. Use **Remove Channel** or **Remove Category** only after checking the selected rows and dependent layouts.

## Use the channels in a layout

1. Open the destination layout in [Layout Editor](../layouts/layout-editor.md).
2. Use [Channel Importer](../layouts/importing-channels.md#import-from-a-source) to select the custom source and import its channels.
3. [Map EPG data](channel-mapping.md) or configure a [dummy guide](dummy-guides.md).
4. [Generate output](output.md) and [test playback](connect-player.md).

Custom channels are maintained in the source editor. For a provider-maintained channel list, use [Adding Playlists](playlists.md) instead.
