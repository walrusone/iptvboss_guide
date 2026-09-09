# Changelog

## 📢 IPTVBoss 3.11.111 → 3.11.118

### 🎬 XC Server player connections and runtime diagnostics

- **Fix:** XC Server player API requests now use only the validated published response generation, so incomplete replacement generations are not served while reloads, restores, or background synchronization are in progress. Requests use a controlled direct fallback when a cached response is unavailable.
- **Improvement:** XC runtime and database transitions coordinate connection pools, EPG preloading, and serving-runtime handoff more consistently, with clearer degraded-state handling when recovery cannot complete.
- **New:** Add diagnostics for database pool usage, connection acquisition, and restore phase timing to help investigate XC Server connection and reload issues.

### 🛡️ Database restore and XC Server safety

- **Fix:** Local, linked, and cloud database restores now validate the replacement data before cutover, coordinate with active XC Server operations, and automatically roll back to the retained database when reload or post-restore validation fails.
- **Fix:** Restore and reload operations keep the previously published XC responses serving until the replacement database reaches cutover, then invalidate stale responses and publish or rebuild data for the restored database. Conflicting mutations receive clear retry responses.
- **Improvement:** Startup detects unfinished restore work and applies recovery handling before reopening the application. Database transitions also wait for active sports-data writes to finish instead of continuing after a quiescence timeout.
- **Fix:** A failed cloud restore on a running XC Server keeps the existing serving runtime when no database replacement occurred. Queued XC restore and reload workflows retain their coordination reservation while cloud checks complete.

### 🧩 AED matching and Layout Editor

- **Fix:** AED sports matching respects the channel’s configured time regex, so an empty channel-level setting no longer unexpectedly inherits a default and changes the keep-period window behavior.
- **Improvement:** Refreshing AEDs for multiple layout groups batches the EPG programme-card update, reducing repeated UI work and keeping the selected channel preview current after the refresh.

## 📢 IPTVBoss 3.11.110

### 🏟️ Dummy Guide inventory

- **New:** Open **Dummy Guide Inventory** from the **Sources** menu to manage basic dummy channels and AEDs in one table, with usage counts, layout/group locations, sports and leagues, today's event counts, fallback AEDs, and last-updated information.
- **New:** Filter entries by type and used/unused status, or search by EPG ID, original ID, or name. Double-click a row to edit it.
- **Improvement:** Add basic dummies or AEDs, import AEDs, export selected AEDs, and delete selected entries from the inventory. Deletion warns when selected entries are used by layouts.
- **New:** Sources Manager shows Dummy Guide totals for basic dummies, AEDs, assigned and unused AEDs, sports AEDs, today's events, and Custom Sports groups and channels. Select the unused-AED count to open the inventory filtered to unused AEDs.

![Edit Dummy EPG Channels inventory with filters, usage locations, and AED details](<3.11.108/Edit Dummy EPG Channels.png>)

### ☁️ XC Server

- **Fix:** XC Server continues serving requests from the published data while a background noGUI sync runs. Requests are briefly paused only during the final data handoff or other database transitions.

## 📢 IPTVBoss 3.11.96 → 3.11.107

### 🧭 Sources and layouts

- **New:** Sources Manager inventory now separates channel and category totals for **TOTAL**, **LIVE**, **VOD**, and **SERIES**, with added/removed provider counts that open content-specific change lists.
- **Improvement:** Sources Manager inventory, access/expiry, and sync-history sections use a responsive layout and remember their expansion state.
- **New:** Layout Editor group options include **Ignore Empty Group Health Check** for groups that are intentionally empty.
- **Improvement:** Layout Manager health recognizes XC output and uses relevant noGUI synchronization history when reporting output status.
- **Improvement:** Adding a valid M3U or Xtream Codes source with no loaded categories opens category management before the source can be saved.

### 🏟️ AED, EPG, and output

- **Fix:** AED refreshes retry eligible no-match channels after source synchronization and later sports-data refreshes, while preserving valid fallback assignments.
- **Fix:** AED assignments and fallback-chain changes are persisted more reliably, including bulk **Revert to Provider Name** operations.
- **Fix:** AED/XMLTV generation handles no-event, after-event, overnight cutoff, and output-horizon cases more consistently.
- **Fix:** Source synchronization and output preparation wait for committed sports data and database transitions before publishing AED-dependent results.

### ☁️ XC Server and cloud synchronization

- **Fix:** Shared cloud locks, XC Server shutdown, backups, reload coordination, and database transitions are handled more safely, including malformed or stale lock recovery paths.
- **Improvement:** Request diagnostics are quieter, avoid printing raw response bodies, and redact full source URLs from logs.

### 🌐 Networking

- **Improvement:** Large HTTP downloads request gzip/deflate compression and transparently write the decoded response, reducing transfer size and provider throttling for supported endpoints.

## 📢 IPTVBoss 3.11.95

### 🧩 Layout Editor and layout health

- **New:** Filter Layout Editor channel lists by **Missing EPG** and **Missing Logo**, including combined use with channel-name search.
- **New:** Layout Manager channel metrics show missing-logo counts separately for **LIVE**, **VOD**, and **SERIES**, and open Layout Editor focused on the matching channels when selected.
- **New:** Layout Manager attention checks can flag missing live, VOD, or TV logos independently.
- **Fix:** Channel-list filtering and sorting now keep the displayed and editable channel collection in sync; reordering and drag-and-drop importing are locked while a filtered or health-focused list is active.

## 📢 IPTVBoss 3.11.88 → 3.11.94

### 🧭 Layout and source management

- **New:** Layout Manager includes a per-layout health dashboard with status, group and channel inventory, EPG coverage, output state, sync history, and assigned-user counts.
- **New:** Layout Manager metrics and warning cards can open Layout Editor with the related layout content in focus.
- **New:** Layout Manager attention checks can be enabled or disabled independently in **Settings** → **IPTVBoss Settings** → **Layout Manager**.
- **Improvement:** Layout Manager settings are grouped into collapsible **General**, **Output & Sync**, and **Advanced / Custom** sections, with a responsive split layout for smaller windows.
- **New:** Sources Manager separates **IPTV Sources** and **EPG Sources** into tabs and shows health status in source rows and selected-source headers.
- **New:** Source details are grouped into collapsible inventory, access/expiry or output/matching, and sync-history sections. Expansion choices are remembered.
- **Improvement:** Source and EPG lists show the last successful sync and health state, while detail cards expose channel/category counts, provider access, user summaries, mapping options, and output history.

### 🏟️ AED and sports data

- **Fix:** Eligible AED sports channels with no match are retried after a successful source synchronization as well as after a later sports-data refresh.
- **Fix:** Startup source synchronization and XC response preparation wait for committed sports data and the active database transition before publishing or saving AED-dependent results.

### ☁️ Cloud synchronization

- **Fix:** Shared cloud lock updates use a dedicated provider upload path, so heartbeat/lease writes do not trigger foreground output-link or progress side effects. Dropbox and Google Drive lock updates now report failures explicitly.

## 📢 IPTVBoss 3.11.87

### 🧩 Layout Editor

- **New:** Display the full programme preview inline below the previous, current, and next programme cards. Select a card to inspect its artwork, title, time, and description.
- **New:** Configure full-preview overlays, inline preview, and preview size from the Layout Editor settings.
- **Improvement:** Channel naming options now support the universal-name **Ignore Name Changes** control, including applicable multi-channel selections and mixed values.

### 🏟️ AED and EPG

- **New:** Configure the signing-off overnight cutoff hour in AED Defaults and in individual AED/advanced dummy channel settings.
- **Improvement:** AED refreshes are coordinated so dependent event and sports data is available before eligible channels are updated.
- **Fix:** No-event and after-event output, signing-off behavior, EPG browser viewing, and late-arriving sports matches are handled more consistently.

### ☁️ XC Server and synchronization

- **New:** Configure the XC Server listener port through the server settings, `-xc-port`, or `IPTVBOSS_XC_PORT`.
- **Improvement:** Cloud-provider locking, backup publication, reload notifications, cache generation, and database transitions are coordinated more safely across paired installations.

## 📢 IPTVBoss 3.11.79 → 3.11.85

Here’s what changed since 3.11.78:

### 🧩 Layouts

- **New:** Enable **Remove Empty Layout Groups After Source Sync** per layout. After a successful source sync, groups with no layout channels can be removed automatically; groups that still contain channels are kept.
- **New:** Choose how each layout channel gets its name: **USE PROVIDER NAME**, **USE UNIVERSAL NAME**, or **USE LAYOUT NAME**.
- **New:** **USE LAYOUT NAME** stores a custom channel name for one layout without changing the shared source channel name in other layouts.
- **Fix:** Layout-channel ID collisions are recovered automatically when channels are imported or copied.

### ☁️ XC Server and cloud sync

- **Fix:** GUI unpairing now removes the local pairing even when XC Server is temporarily unreachable, while clearly reporting when server-side revocation still needs to complete.
- **New:** Bootstrap recovery can distinguish between keeping the current pairing, unpairing for a later re-pair, and performing a full server reset.
- **New:** XC output uses exact response variants with generation-aware publication and direct serving fallback. This reduces rebuild memory and file-work while preserving authorization-specific responses.
- **Fix:** Rebuilds and database transitions no longer publish incomplete or empty XC responses, and stale generations remain available until a replacement is ready.
- **Fix:** XC Server reload notifications are sent only after the required cloud backup has completed successfully, reducing unnecessary reload attempts.
- **Improvement:** XMLTV output can serve the compressed guide when the client advertises gzip support.

### 🏟️ Sports and EPG

- **Fix:** Sports channels, including ESPN+ matches whose event data arrives late, can retry eligible no-match assignments after a later sports-data refresh.
- **Fix:** Layout-specific channel names are now used consistently in playlist, guide, player, sorting, and preview output.

If you run into an issue after updating, include the exact IPTVBoss version, operating system, reproduction steps, and sanitized logs when asking in [Discord](https://discord.gg/s3kpjP8EgR) or opening a [support ticket](https://members.bosstees.net/).
