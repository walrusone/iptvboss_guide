# Changelog

## 📢 IPTVBoss 3.11.138

### 🏟️ Custom Sports and AED

- **New:** Custom Sports groups can enable an exclusions list in **Sports Settings**. Exclusions are case-insensitive literal keywords or phrases matched against the original provider channel name.
- **New:** Excluded channels are removed before AED lookup, sports classification, sorting, and custom presentation numbering. This is independent of **Remove Channels without Events**, and the source channels remain available to other groups and layouts.
- **New:** Custom Sports presentation settings can preserve linear channels with their original names and logos, and eligible individual channels can use **Ignore Custom Presentation**. Excluded or ignored channels do not consume a presentation number.
- **Fix:** Custom Sports previews and generated output now use the same final filtered order for presentation names and logos.
- **Fix:** AED event keep-window checks honor the configured event duration for same-day events, including zero-hour configurations.
- **Fix:** Sports team matching handles Manchester United and Manchester City derby names without incorrectly rejecting either team.

### 💾 Backups, synchronization, and dialogs

- **Improvement:** User backup and cloud publication workflows now use verified snapshots, mutation journaling, ownership handoff, and deferred retry handling so active edits are not overwritten or published from a stale database.
- **Fix:** Console and cloud user mutations coordinate their publication handoff and report unresolved publication blockers instead of silently losing changes.
- **Improvement:** Large scrollable dialogs now choose a more useful initial viewport and window bounds during startup.

### 🧭 Sources and playlist synchronization

- **New:** The source Category Manager now provides **All**, **Stale**, **Active**, and **Disabled** status filters. Status filtering combines with the per-content-type category search, and stale status is independent of whether a category is active.
- **New:** M3U and Xtream Codes source settings include **Disable NoGUI user checks**, allowing automatic credential and expiry checks to be skipped for an individual source during NoGUI synchronization.
- **Improvement:** At-risk NoGUI credential refreshes are limited to once every 24 hours per credential and paced per provider host, reducing repeated provider requests during scheduled runs.
- **Fix:** Adding new channels during synchronization is faster for large playlists, including source and layout processing for newly discovered channels.
- **Improvement:** Category statistics in Sources Manager are now clickable: total, active, and stale values open Category Manager for the selected source with the matching content type and filter.

### 🧩 Linked layouts and playback

- **Fix:** Series playback from linked layouts now finds the single enabled XC provider from linked series groups when no provider was previously remembered. Requests remain rejected when no provider or multiple providers can be identified.

### 🏟️ AED and sports output

- **Fix:** AED startup checks repair event-assignment foreign keys left pointing at an invalid table-copy target, clear invalid event references, and queue affected assignments for refresh instead of failing with an integrity-constraint violation.
- **Fix:** `{league}` now uses the league’s **Display Name**, falling back to **Name** when Display Name is empty or null.

### 🔐 XC Server API

- **Improvement:** External API-key clients can now retrieve the raw `openapi.json` document from `/openapi`. The `/swagger` interactive UI and its web assets still require an authenticated administrator console session.
- **Improvement:** The OpenAPI document reflects the current user automation routes, including layout-specific password set/regenerate operations and the retired bulk password-reset route, which returns `410 Gone`.
- **Fix:** XC Server user-mutation responses no longer report a misleading M3U-output-unavailable warning solely because source data is not loaded in the server process.

## 📢 IPTVBoss 3.11.127

### 🏟️ Teams, AED, and EPG output

- **Fix:** Team-based AED exports now preserve eligible sequential fixtures and stay aligned with the Layout Editor preview, without appending an incorrect no-event fallback schedule.
- **Fix:** AED refresh sessions always close and release active ownership when processing or sports-data barrier failures occur, while preserving the original failure details.
- **Fix:** EPG parsing now reports failures safely when an exception has no message, and generated sequential output is validated before it replaces no-event handling.

### 🖥️ NoGUI reliability and diagnostics

- **Improvement:** Standalone NoGUI runs now record phase and periodic memory diagnostics, including heap, non-heap, buffer, thread, and garbage-collection details. Spawned NoGUI processes also report their PID and exit code.
- **Fix:** NoGUI markers now use ownership-aware acquisition and verified stale-marker recovery, preventing concurrent runs from removing another process’s marker.
- **Fix:** Fatal NoGUI failures perform best-effort cleanup, skip backup/publication, retain uncertain markers and cancellation requests, and return a nonzero exit status when appropriate.
- **Fix:** EPG sync failures and logger failures are reported through an independent emergency path instead of causing recursive logging failures.

### 🪟 Dialog presentation

- **Fix:** Dialog ownership is applied only to active windows with an attached scene, avoiding owner initialization errors during window transitions.

## 📢 IPTVBoss 3.11.126

### 🔐 Per-layout XC passwords

- **Improvement:** Each user’s assigned XC-enabled layout now has its own password. Passwords are case-sensitive and unique across that user’s layouts; existing credentials are migrated without changing already-published links.
- **New:** Desktop **Manage Users** can edit or generate the password for the layout selected in **Layout Preview**. The XC Server console provides the same **Edit Layout Password** workflow.
- **New:** User API reads and mutations support layout-specific passwords, including targeted set and regenerate operations. The former bulk reset endpoint is retired.

### 🏟️ AED and channel output fixes

- **Fix:** Custom Sports TXT lookup names remain parsing inputs and no longer replace a channel’s universal output name when no channel-name rule is configured. Blank or invalid regex matches fall back to the channel name.
- **Fix:** Removing AED EPG assignment clears the durable assignment, queued refresh request, and orphaned regex event state together.
- **Improvement:** AED refresh ownership and cancellation remain consistent while an editor closes or the application shuts down. Pending work can be resumed without leaving the GUI blocked.

### 🏟️ Sports data and AED selectors

- **Fix:** Startup refreshes the sports dataset even when no deferred source-sync queue is needed, so AED selectors are ready after startup.
- **Fix:** League selectors use the best available league label when a provider does not supply a preferred display name.

### 🪟 Dialog presentation

- **Improvement:** Dialogs and alerts open attached to the active application window and follow the selected light or dark theme, including dialogs opened from secondary windows.

## 📢 IPTVBoss 3.11.122

### 🏟️ Resumable AED refresh

- **New:** **Sources** → **AED Refresh…** offers **Refresh All** for all configured AED channels and **Refresh Pending** for queued work left by an earlier paused or incomplete refresh.
- **New:** AED refresh progress can be cancelled cooperatively, and the progress view identifies the current AED and visited-channel count.
- **New:** The AED refresh completion dialog reports hits, misses, explicit no-event results, still-pending work, per-AED status, and expanded diagnostic counts for work that failed or was not processed.
- **Improvement:** Manual AED refreshes use current sports data and more consistent event matching, including tester refreshes and forced result recalculation.

### 🔄 GUI source synchronization

- **Improvement:** GUI startup now loads saved sources first, then runs eligible **Sync on Start** sources through a deferred sequential queue after the main startup and database or cloud transition work is ready.
- **New:** Selected-source syncs, **Sync All Sources**, startup syncs, and post-save syncs support cooperative cancellation. Cancelling records the active source as cancelled and skips the remaining sources in that batch.
- **Improvement:** Editing an existing source saves settings before optionally prompting **Sync now?**; the prompt is shown only when the changes affect source synchronization.

## 📢 IPTVBoss 3.11.119

### 🏟️ AED fallback chains and multi-event output

- **Improvement:** AED fallback chains now evaluate hours-back, hours-ahead, today-only, and 24-hour tracking windows from one consistent output-time anchor, including fallback and linked-event resolution.
- **Fix:** Custom sports channels can resolve and render multiple upcoming fixtures from configured TXT sources in guide output, previews, and GUI or noGUI refreshes.
- **Fix:** Events outside the configured keep period, stale date-less matches, and fallback cycles are excluded more consistently from generated guide output.

### 🧩 Dummy Guide inventory

- **Fix:** Dummy Guide Inventory usage now follows multi-level AED fallback chains and original/output aliases with cycle protection. Assigned and unused AED totals now reflect the AEDs actually used by layouts.

### ☁️ XC Server and database synchronization

- **Fix:** Queued database reloads now use the coordinator's reserved restore path, report reload warnings, and safely handle unavailable server or logging state during cloud/local synchronization.

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
