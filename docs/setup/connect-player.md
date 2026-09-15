# Connect a Player

Generate output for the intended layout before configuring the player. If output is assigned to a user, confirm that the user, layout, and required provider credentials are enabled in [Desktop Output Users](../layouts/users.md) or [XC Server Users](../server/console/users.md).

## Choose the connection type

| Connection | Values needed | Where to get them |
| --- | --- | --- |
| Local files | M3U playlist and XMLTV guide files | The folder selected in [Layout Output Settings](../layouts/output-settings.md) |
| Cloud links | M3U URL and EPG/XMLTV URL | **Output** → **View Cloud Links** after [cloud output generation](output-links.md) |
| XC Server login · PRO | Public server address and the XC username/password for the assigned layout | The user's assigned XC-enabled layout in [user management](../layouts/users.md#user-output-links) |

Keep output links and login details private. An XC player's credentials are the user's layout credentials; the administrator login and desktop pairing code are for managing the server.

## M3U and XMLTV

1. Add a playlist in a player that supports M3U input.
2. Select the generated M3U file or enter its cloud URL.
3. Add the matching XMLTV file or EPG URL as that playlist's guide source.
4. Refresh the playlist and guide in the player.

Local files must be accessible to the device running the player. If it cannot open files on the IPTVBoss computer, use a supported hosted-output workflow. When users share the same guide data, review [Universal EPG](universal-epg.md).

## XC Server login

1. Complete [XC Server setup](../server/first-connection.md).
2. Enable XC output on the intended layout and assign it to the user.
3. Obtain that layout's XC login details from user management.
4. Choose the player's Xtream Codes/XC connection option.
5. Enter the public server address, including its port when required, and the layout's XC username/password.
6. Refresh the player's content and guide.

Use the player-facing server address rather than the `/boss.php` administration-page URL. Changing a layout's password requires updating the credentials in players using that layout.

## Verify the result

1. Confirm the expected groups and channels appear.
2. Play an imported channel.
3. Check programme information on a channel with a saved EPG mapping.
4. For XC delivery, review [User Activity](../server/console/activity.md) if you need to confirm successful server requests.

For missing channels, review source category selection and layout import. For missing programmes, check the [EPG mapping](channel-mapping.md), regenerate output, and refresh the player. See [Common Problems](../troubleshooting/common-problems.md) if the result still differs from the layout.
