# Build Your First Working Setup

This tutorial takes a new desktop installation from an empty database to cloud-hosted M3U playlist and EPG (XMLTV) links that you can use in a player. It uses the core Free workflow; cloud playlist/EPG publishing does not require Pro. Cloud database backup, automation, advanced tools, and XC Server are optional next steps.

Before you begin, have these items ready:

- an authorized M3U playlist or Xtream Codes provider connection;
- an external EPG URL that contains guide data for at least some of your channels;
- a Dropbox or Google Drive account that can host the generated output;
- a player that can load an M3U playlist and EPG from URLs.

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
4. Open **Sources** → **Sources Manager** and open the **IPTV Sources** tab.
5. Select the source and select **Sync** in the source action bar.
6. Wait for synchronization to finish and confirm that the source contains channels.

Start with a small channel set. You can enable more categories after the complete workflow works.

## 3. Add and synchronize an EPG source

1. Open **Sources** → **Add EPG**.
2. Leave **Source** set to **Custom**.
3. Enter a descriptive name and the external EPG URL.
4. Save the source.
5. In **Sources Manager**, open the **EPG Sources** tab, select the EPG source, and select **Sync** in the source action bar.
6. Wait for synchronization to finish and confirm that EPG channels were imported.

See [Adding an EPG Source](../setup/epg-sources.md) for time-zone, logo, and EPG layout options.

## 4. Configure cloud publishing

1. Open **Settings** → **IPTVBoss Settings**.
2. Expand **Cloud & Backups** and enable the cloud settings.
3. Select **Dropbox** or **Google Drive**.
4. Follow the matching tab in [Cloud Provider Setup](../settings/cloud-providers.md) to create the required provider app and enter its credentials.
5. Select **Authorize** and complete the provider's authorization flow.
6. Save the settings.

This authorizes IPTVBoss to publish playlist and EPG files. It does not require or enable Pro cloud database synchronization or backup. Keep app secrets, authorization codes, and generated output links private.

## 5. Create and enable a layout

1. Open **Layout** → **Layout Manager**.
2. Select **Add New Layout**.
3. Enter a descriptive name, such as `First Test`.
4. Enable M3U and EPG output for the layout.
5. Select a **Cloud Provider Folder** for the provider you authorized.
6. Make sure the layout itself is enabled.
7. Select **Save Layout**.

The layout is the publishable channel list. Adding sources alone does not place their channels into output.

## 6. Import channels into the layout

1. Open **Layout** → **Layout Editor**.
2. Select the layout you just created.
3. Select **Add Channels** to open **Channel Importer**.
4. Use **Source Import** mode and select your playlist source.
5. Select one or more Live groups or channels.
6. Select the matching import action.
7. Close the importer and confirm that the channels appear in the destination layout.

See [Editing a Layout](../layouts/layout-editor.md#import-channels-with-channel-importer) for selection, duplicate, and grouping options.

## 7. Map EPG data

1. Select a channel that should have guide data.
2. In **Channel Options**, expand **EPG Mapping**.
3. Open **EPG Search Options** and enable the external EPG source you added.
4. Select **Manual** and choose the matching EPG channel.
5. Confirm the source and channel identifier, then select **Save Channel(s)**.
6. Repeat for several test channels.

Choose a mapping only when the channel identity is reliable. An incorrect guide is more misleading than a missing one. See [Mapping Channels](../setup/channel-mapping.md) for the complete mapping workflow.

## 8. Generate cloud output

1. Return to **Layout Manager** and confirm that the layout health dashboard, M3U output, EPG output, and cloud-provider folder are correct and saved.
2. Open **Output**.
3. Select **Current Layout M3U & EPG**.
4. Wait for generation and upload to finish.
5. Select **Output** → **View Cloud Links**.
6. Find the M3U and EPG links for the layout you created.
7. Copy the two links without exposing them in a screenshot, issue, or support message.

Do not use an all-layout output action while testing one layout.

## 9. Test the result

1. Add the generated cloud M3U link to your intended player.
2. Configure the generated cloud EPG link as that playlist's guide source.
3. Refresh the playlist and guide in the player.
4. Confirm that an imported channel plays.
5. Confirm that a mapped channel shows the expected programme information.

If the cloud links are missing, confirm the provider authorization and the layout's cloud-provider folder, then regenerate the current layout output. If channels are missing, review the source categories and layout import. If guide data is missing, confirm that the EPG synchronized, the channel mapping was saved, the EPG link is assigned to the playlist in the player, and the output was regenerated.

## Next steps

After the cloud links work, you can:

- [organize and rename layout content](../layouts/layout-editor.md);
- [configure additional output settings](../layouts/output-settings.md);
- [create desktop output users](../layouts/users.md);
- [automate synchronization](../settings/automation.md).
