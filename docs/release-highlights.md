# Release Highlights

This summary groups the recent changes documented in the [build-by-build changelog](changelog.md). For an existing installation, start with [Update from 3.11.16](getting-started/update-from-3.11.16.md). Stable downloads are available from [iptvboss-release](https://github.com/walrusone/iptvboss-release/releases/latest).

## New in 3.12.8

- Choose a separate retention period for [automatic stale source-group cleanup](setup/playlists.md#automatically-remove-stale-source-groups) in M3U and XC sources; the option defaults off.

- Set up [XC Player Pairing (Beta)](server/console/player-pairings.md) to hand a compatible player its assigned XC layouts using a single-use code.
- Publish a per-user M3U configuration URL through [Boss Player Output (Beta)](setup/boss-player-output.md). Both player features require Pro and a compatible beta player.
- Exclude intentional missing mappings, missing logos, or empty groups through [group health check options](layouts/layout-editor.md#group-health-check-options).
- Choose when to [load external EPG programmes for preview](setup/epg-sources.md#control-external-programme-preview-loading), and resolve [missing-cache output warnings](setup/output.md#epg-sources-that-require-synchronization).
- Understand [AED tester matches outside the output window](features/aed-maintenance.md#understand-matches-outside-the-output-window). See the [3.12.8 changelog](changelog.md) for AED, dummy-guide, editor, and runtime fixes.

## Sources and layouts

- Review source inventory and synchronization history in [Sources Manager](setup/sources-manager.md).
- Filter [playlist categories](setup/playlist-categories.md) by active, disabled, and stale status.
- Use [layout health](layouts/layout-manager.md#read-layout-health) and missing-content filters to find unmapped channels, missing logos, and empty groups.
- Choose provider, universal, or layout-specific [channel names](layouts/layout-editor.md#choose-the-channel-name-source).
- Share groups through [linked layouts](layouts/linked-groups.md) and review [automatic empty-group cleanup](layouts/layout-manager.md#remove-empty-groups-after-source-sync).

## EPG and sports

- Find dummy guides and AED usage in [Dummy Guides and Inventory](setup/dummy-guides.md).
- Test and resume AED refreshes through [Manage and Refresh AEDs](features/aed-maintenance.md).
- Configure sports exclusions, favourites, sorting, and numbered presentation in [Custom Sports Groups](features/custom-sports.md).
- Use [TXT fixture names](setup/custom-sports-channel-names.md) as matching input, and control rendered names through [AED templates](features/aed-reference.md).

## Automation and server workflows

- Refresh provider expiry from [Desktop Output Users](layouts/users.md#refresh-provider-expiry) or [XC Server Users](server/console/users.md#refresh-provider-expiry), and review how [XC account expiry](layouts/users.md#xc-account-expiry) follows each layout.
- Understand [NoGUI cancellation status and interrupted-run recovery](settings/automation.md#nogui-status-and-cancellation).
- Review [NoGUI credential checks](settings/automation.md#automatic-nogui-user-checks) and the per-source opt-out.
- Choose between [desktop, external, and server scheduling](settings/sync-overview.md).
- Follow [First XC Server Connection](server/first-connection.md) for the server workflow, including pairing, users, and player verification.
- Manage layout-specific XC passwords in [Desktop Output Users](layouts/users.md#xc-passwords-and-activity) or [XC Server Users](server/console/users.md).
- Use [API Keys](server/console/api.md) and [API documentation](server/console/swagger.md) for external automation.

## Before updating

Preserve a [database backup](settings/backups.md), follow the [update procedure](getting-started/updating.md), and verify sources, layouts, and output afterward. Consult the [changelog](changelog.md) for fixes tied to a specific build.
