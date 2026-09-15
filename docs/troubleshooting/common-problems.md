# Common Problems

Use these checks before opening a support ticket. If a check changes data or requires a restore, stop and create a backup first.

## IPTVBoss will not start

1. Confirm that another IPTVBoss process is not already running.
2. Restart the computer if the application closed unexpectedly.
3. Confirm that the application data location is accessible and has free disk space.
4. Review the most recent startup log.
5. Record the version and operating system.

Do not delete the database to solve a startup problem.

## A playlist will not synchronize

Check the source URL, provider availability, username, and password. Confirm that the source type matches the provider information:

- Genuine M3U playlist URL or local M3U file: **Add M3U Source**
- Standard Xtream Codes `get.php?username=...&password=...` link: extract the server address, username, and password, then use **Add XC Source**

Review the source log and try again only after confirming the provider is reachable.

## An EPG source has no channels

Confirm that the URL returns the expected EPG format, that synchronization completed, and that the source contains channels. If the source is private, do not paste its URL into a public ticket.

## Channels have no guide data

1. Confirm that the EPG source synchronized successfully.
2. Open the correct layout in **Layout Editor**.
3. Confirm that the channel has an EPG source and identifier.
4. Check that the layout is configured to output EPG data.
5. Generate the EPG again.

## Layout Manager shows Needs attention

Select the layout and read the reason beside its **Needs attention** badge. The dashboard can identify unmapped live channels, missing live/VOD/TV logos, missing source channels, empty groups, broken linked groups, duplicate custom channel numbers, output failures, output currently running, or output that has never been generated.

Select the affected metric to open Layout Editor with the related groups or channels in focus. If a condition is intentional, open **Settings** → **IPTVBoss Settings** → **Layout Manager** and disable only the corresponding attention check. Changing the check does not change the layout content or output.

## Sources Manager shows Needs attention

In Sources Manager, review the selected source’s health reason and **Sync history**. A playlist source may need attention because its last sync was skipped or is running, a category needs attention, it has not been synced yet, or its XC account is expired or close to expiry. An EPG source may need attention because it has not been synced yet or its last sync was skipped or failed.

Use **Inventory** to confirm that the source contains the expected channels, and use **Access & expiry** for XC account and user details. Correct the source settings or provider account, then run a single-source sync and review the status again.

## A sports channel has no current event

Sports event data may arrive after the source channel has already synchronized. When an AED-based sports channel has no matching event, IPTVBoss keeps the channel available and retries eligible no-match assignments after a successful source synchronization or during a later sports-data refresh. Confirm that the AED is assigned, the provider channel name is correct, and the sports data refresh completed before changing the layout or deleting the channel.

For ESPN+ channels that were published before their event data was available, wait for the source sync or next sports-data refresh to complete and then review the channel in **Layout Editor**. If the application is starting or reloading a synchronized database, allow the sports-data readiness step to finish before judging the result. Use [Advanced EPG Dummies](../features/aed.md) when the provider name requires a different matching pattern.

## Output is missing channels

Confirm that the intended layout is selected, the layout is enabled, and the channels are assigned to the expected groups. If you used an all-layout output action, confirm that you are reviewing the correct output file.

- <span id="server-did-not-reload-after-a-client-update"></span>[Server did not reload after a client update](../server/troubleshooting.md#server-did-not-reload-after-a-client-update)
- <span id="xc-output-is-rebuilding-or-serves-an-older-response"></span>[XC output is rebuilding or serves an older response](../server/troubleshooting.md#xc-output-is-rebuilding-or-serves-an-older-response)

## The application shows a locked feature

Some EPG, layout, source, and advanced editing features depend on the account plan. Confirm the account status in [Free vs Pro](../getting-started/free-vs-pro.md) and [IPTVBoss Pro Settings](../settings/pro.md) before treating a locked control as an application error.

## When to open a ticket

After collecting the version, operating system, steps to reproduce, and sanitized logs, ask in the [IPTVBoss Discord](https://discord.gg/s3kpjP8EgR) or open a [support ticket through the member portal](https://members.bosstees.net/) for account-specific or private issues.
