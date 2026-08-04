# Frequently Asked Questions

## Does IPTVBoss provide IPTV streams?

No. IPTVBoss is an editor that imports and manages playlist and EPG sources supplied by the user. Use only sources that you are authorized to access.

## What is the difference between an M3U source and an API source?

An M3U source uses a complete playlist URL or local `.m3u` file. An API source uses provider connection details such as a server address, username, and password.

## Why can I see a feature but not use it?

Some features depend on the account plan. See [IPTVBoss Pro and Account Access](settings/pro.md) for the confirmed Pro-only feature categories and account-access workflow.

## Why is my EPG empty?

Confirm that the EPG source synchronized successfully, contains channels, and is assigned to channels in the selected layout. Then regenerate the layout’s EPG output.

## Where are the logs?

Open [Logs and Diagnostics](troubleshooting/logs.md) and select **View Logs**. IPTVBoss also stores log files in a `logs` directory under the application data directory.

## Can I update without losing my layouts?

Yes, a normal update should preserve the existing database and configuration. Create or confirm a backup before updating, close IPTVBoss, install the new version, and verify the layouts after startup.

## What should I include when asking for support?

Include the IPTVBoss version, operating system, exact steps to reproduce the problem, approximate failure time, relevant settings, and sanitized logs.

For general questions, [join the IPTVBoss Discord](https://discord.gg/s3kpjP8EgR). For account, billing, private, or sensitive issues, [open a support ticket through the member portal](https://members.bosstees.net/).

!!! warning
    Never include passwords, playlist URLs containing credentials, tokens, license keys, or private customer information.
