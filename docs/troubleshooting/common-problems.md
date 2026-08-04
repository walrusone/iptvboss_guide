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

- Complete playlist URL: **Add M3U Source**
- Server, username, and password: **Add API Source**

Review the source log and try again only after confirming the provider is reachable.

## An EPG source has no channels

Confirm that the URL returns the expected EPG format, that synchronization completed, and that the source contains channels. If the source is private, do not paste its URL into a public ticket.

## Channels have no guide data

1. Confirm that the EPG source synchronized successfully.
2. Open the correct layout in **Layout Editor**.
3. Confirm that the channel has an EPG source and identifier.
4. Check that the layout is configured to output EPG data.
5. Generate the EPG again.

## Output is missing channels

Confirm that the intended layout is selected, the layout is enabled, and the channels are assigned to the expected groups. If you used an all-layout output action, confirm that you are reviewing the correct output file.

## The application shows a locked feature

Some EPG, layout, source, and advanced editing features depend on the account plan. Confirm the account status in [IPTVBoss Pro and Account Access](../settings/pro.md) before treating a locked control as an application error.

## When to open a ticket

Open a ticket through the [IPTVBoss member portal](https://members.bosstees.net/) after collecting the version, operating system, steps to reproduce, and sanitized logs.
