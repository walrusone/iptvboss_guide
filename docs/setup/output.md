# Creating Output

After sources and layouts are configured, generate the playlist and guide files that your player or hosting provider will use.

## Prepare the layout

Before generating output:

1. Open [Layout Manager](../layouts/layout-manager.md).
2. Select the intended layout.
3. Confirm that the layout is enabled.
4. Confirm that the required channels and EPG mappings are saved.
5. Confirm the [Layout Output Settings](../layouts/output-settings.md), output folder, or cloud-provider settings.

## Generate output for the current layout

Open the **Output** menu and choose the output type you need:

- **Current Layout M3U** generates the playlist for the selected layout.
- **Current Layout EPG** generates the guide data for the selected layout.
- **Current Layout M3U & EPG** generates both outputs together.

![The Output menu](../assets/images/output-menu.png)

Wait for the output operation to finish before opening the files or copying links into a player.

## Generate output for all layouts

Use the all-layout options when you intentionally want to process every enabled layout:

- **All Layouts M3Us** generates playlists for all layouts.
- **All Layouts M3Us & EPGs** generates playlists and guide data for all layouts.

Do not use an all-layout action when you are testing a single layout change.

After generation, follow [Connect a Player](connect-player.md) for local files, cloud links, or XC login. For separate viewer accounts, see [Desktop Output Users](../layouts/users.md).

## EPG sources that require synchronization

If output reports **EPG sources require sync**, the named sources had unavailable cached guide data and were omitted from that output. Synchronize those sources in [Sources Manager](sources-manager.md), regenerate the affected guide output, and refresh it in the player. Changing **Automatically load programs** does not disable output and is not a substitute for synchronizing missing source data.

## Boss Player Output (Beta) <span class="pro-badge">PRO</span>

When enabled, [Boss Player Output](boss-player-output.md) publishes each eligible user's configuration after the output batch completes. For initial setup, generate **All Layouts M3Us & EPGs** so the required cloud links exist. Review publication errors before distributing the user's configuration URL.

## Review output

1. Open the configured output folder or cloud-provider destination.
2. Confirm that the expected M3U and/or EPG file exists.
3. Open the file only if doing so does not expose private URLs or credentials.
4. Test the output in your intended player.
5. If the player shows no guide data, return to [Mapping Channels](channel-mapping.md) and verify the EPG assignments.

!!! note
    Output filenames and destinations depend on the layout settings. Do not assume that every layout writes to the same folder.

!!! note
    Menu labels may change between desktop releases. Use [Cloud Output Links](output-links.md) when the layout publishes cloud-hosted links.
