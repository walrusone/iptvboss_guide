# Feature Overview

IPTVBoss is a desktop editor for importing IPTV and EPG sources, organizing channels, and generating personalized output.

## Sources

The **Sources** menu provides workflows for:

- Adding M3U and API sources.
- Adding and synchronizing EPG sources.
- Managing imported categories and channels.
- Reviewing source synchronization.
- Applying source and channel cleanup tools.

Start with [Adding a playlist](../setup/playlists.md) and [Adding an EPG source](../setup/epg-sources.md).

## EPG and channel editing

IPTVBoss can search EPG data, assign EPG channels, update logos, edit channel names, and create dummy or advanced dummy EPG entries for supported workflows.

Use [Mapping Channels](../setup/channel-mapping.md) for the standard mapping workflow.

For event-aware channel naming, logos, and sports metadata, see [Advanced Event Definitions](aed.md). For sports-specific channel filtering and ordering, see [Custom Sports](custom-sports.md).

## Layouts

Layouts let you create different channel arrangements from the same imported sources. You can organize groups, reorder channels, assign EPG data, configure output, and manage separate audiences.

See [Layouts](../layouts/index.md).

## Output and hosting

The **Output** menu generates M3U playlists, EPG files, combined output, and output for all layouts. Layouts can also be configured for cloud-provider or server workflows where available.

See [Creating Output](../setup/output.md).

## Automation and diagnostics

IPTVBoss includes synchronization schedules, database backup and restore workflows, log viewing, and optional server or headless capabilities. These features should be configured only after the basic desktop workflow is working.

## Basic and Pro capabilities

Basic accounts provide the core editor workflow with limits on sources, EPG sources, users, and layouts. Pro access may add built-in EPGs, automatic mapping, advanced EPG tools, higher limits, cloud and output features, and other capabilities.

The exact availability and limits depend on the account and release. Treat a locked menu item as a plan or authorization question first, then review the account settings or contact support.

!!! note
    This overview describes the IPTVBoss 3.11 desktop workflow. Feature names and availability may change in later releases.
