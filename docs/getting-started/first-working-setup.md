# Build Your First Working Setup

This tutorial takes a new desktop installation from an empty database to local M3U playlist and EPG (XMLTV) files that you can test in a player. It uses the core Free workflow; Pro features, cloud publishing, users, and XC Server are optional next steps.

Before you begin, have these items ready:

- an authorized M3U playlist or Xtream Codes provider connection;
- an external EPG URL that contains guide data for at least some of your channels;
- a local folder where IPTVBoss can write output;
- a player that can load an M3U playlist and its EPG.

Treat provider addresses, usernames, passwords, output links, and tokens as private credentials.

## 1. Install and open IPTVBoss

1. [Install IPTVBoss](installation.md) for your operating system.
2. Start the application and [complete the first-run checks](first-run.md).
3. Confirm that **Sources**, **Layout**, **Output**, and **Settings** open without an error.

If you are opening an existing database, stop here and back it up before replacing or restructuring its content.

## 2. Add and synchronize a playlist

1. Follow [Adding Playlists](../setup/playlists.md) and choose the M3U or Xtream Codes workflow that matches the information from your provider.
2. Open **Manage Categories**, refresh the provider categories, and enable a small set of Live categories for this first test.
3. Save the source.
4. Open **Sources** → **Sources Manager**.
5. Select the source and select **Sync** in the **IPTV Sources** header.
6. Wait for synchronization to finish and confirm that the source contains channels.

Start with a small channel set. You can enable more categories after the complete workflow works.

## 3. Add and synchronize an EPG source

1. Open **Sources** → **Add EPG**.
2. Leave **Source** set to **Custom**.
3. Enter a descriptive name and the external EPG URL.
4. Save the source.
5. In **Sources Manager**, select the EPG source and select **Sync** in the **EPG Sources** header.
6. Wait for synchronization to finish and confirm that EPG channels were imported.

See [Adding an EPG Source](../setup/epg-sources.md) for time-zone, logo, and EPG layout options.

## 4. Create and enable a layout

1. Open **Layout** → **Layout Manager**.
2. Select **Add New Layout**.
3. Enter a descriptive name, such as `First Test`.
4. Enable M3U and EPG output for the layout.
5. Choose a local **Custom Output Folder** that you can find easily.
6. Make sure the layout itself is enabled.
7. Select **Save Layout**.

The layout is the publishable channel list. Adding sources alone does not place their channels into output.

## 5. Import channels into the layout

1. Open **Layout** → **Layout Editor**.
2. Select the layout you just created.
3. Select **Add Channels** to open **Channel Importer**.
4. Use **Source Import** mode and select your playlist source.
5. Select one or more Live groups or channels.
6. Select the matching import action.
7. Close the importer and confirm that the channels appear in the destination layout.

See [Editing a Layout](../layouts/layout-editor.md#import-channels-with-channel-importer) for selection, duplicate, and grouping options.

## 6. Map EPG data

1. Select a channel that should have guide data.
2. In **Channel Options**, expand **EPG Mapping**.
3. Open **EPG Search Options** and enable the external EPG source you added.
4. Select **Manual** and choose the matching EPG channel.
5. Confirm the source and channel identifier, then select **Save Channel(s)**.
6. Repeat for several test channels.

Choose a mapping only when the channel identity is reliable. An incorrect guide is more misleading than a missing one. See [Mapping Channels](../setup/channel-mapping.md) for the complete mapping workflow.

## 7. Generate local output

1. Return to **Layout Manager** and confirm that the layout, M3U output, EPG output, and local output folder are enabled and saved.
2. Open **Output**.
3. Select **Current Layout M3U & EPG**.
4. Wait for generation to finish.
5. Open the configured output folder and confirm that both the M3U and EPG files exist.

Do not use an all-layout output action while testing one layout.

## 8. Test the result

1. Load the generated M3U file into your intended player.
2. Configure the generated EPG file as that playlist's guide source.
3. Confirm that an imported channel plays.
4. Confirm that a mapped channel shows the expected programme information.

If channels are missing, review the source categories and layout import. If guide data is missing, confirm that the EPG synchronized, the channel mapping was saved, and the EPG output was regenerated.

## Next steps

After the local test works, you can:

- [organize and rename layout content](../layouts/layout-editor.md);
- [configure additional output settings](../layouts/output-settings.md);
- [create desktop output users](../layouts/users.md);
- [publish output through a cloud provider](../settings/cloud-providers.md);
- [automate synchronization](../settings/automation.md).
