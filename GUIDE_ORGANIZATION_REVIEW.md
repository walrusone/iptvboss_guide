# Stable-release guide organization review

Reviewed 15 September 2026. Scope: all 65 guide Markdown pages, navigation, shared XC preview notice, and local page links. Application source was spot-checked for custom sources, dummy-guide inventory, and restore choices. This is an organization review, not a complete feature, entitlement, command, or platform-validation audit.

## Implementation status — first pass

Implemented 15 September 2026:

- Reorganized navigation around EPG, output, sports, scheduling, and XC tasks. Exposed VPS preparation and moved release notes below the task sections.
- Added 12 pages: EPG and sports overviews, Channel Importer, Dummy Guide inventory, AED reference, AED maintenance, AED troubleshooting, scheduling chooser, database backups, database restore, player connection, and first XC connection.
- Reordered layout creation/editing, source details, EPG source setup, sports presentation, desktop XC pairing, and external scheduling.
- Added contextual links across onboarding, source categories, output, user credentials, settings, recovery, and support.
- Corrected the Windows backup instruction to identify the paired desktop dashboard; checked that interface and the Layout Manager output controls against application source.
- Preserved the existing Layout Editor screenshot edit, all existing page URLs, and every prior generated heading anchor. Moved sections retain pointers at their old anchors.

Validation: strict MkDocs build passed using the repository's pinned Material version in a temporary environment. A rendered HTML check passed for 8,787 local links/image references, including fragment targets; comparison against the original pages found no missing old heading anchors. No live server/platform execution or external-link validation was performed.

The remaining structural work listed after the first pass is completed below. The original recommendations and initial-review findings are retained for context.

## Implementation status — second pass

Completed the remaining structural work:

- Added Custom Sources, Linked Layout Groups, Layout File Import and Export, and EPG Formatting and Overrides. Checked procedures against the custom-source editor, layout exporter/importer, linked-group code, and EPG writer.
- Extracted XC runtime configuration, server recovery, server troubleshooting, and application file locations into dedicated pages. Updated incoming links while preserving original page and fragment URLs.
- Consolidated administrator-account instructions into Console Security and Boss Metadata into the console overview. The former pages remain as compatibility entry points outside the sidebar.
- Made the Swagger/API page the owner of specification access and endpoint examples; API Keys now links to it.
- Expanded Link Restore into a procedure using the actual console field labels and restore behavior.
- Added Release Highlights linked to the existing build changelog and task guides. A final stable version number is not asserted until supplied.
- Added contextual links from sources, layout management, output settings, sports, entitlement comparison, and console setup.

Validation: strict MkDocs build passed. The rendered HTML audit checked 10,319 local link/image references without errors. All original and first-pass heading anchors remain available. `git diff --check` passed. The two pages intentionally omitted from navigation are the administrator-account and metadata compatibility pages. No live platform/server execution or external-link validation was performed.

Release decision: keep the current XC preview labels and alpha Docker defaults, as requested. The only release-day editorial item is to add the final stable version/distribution details to Release Highlights when available. Existing macOS/Windows platform-testing notices remain in place; this documentation work does not establish platform validation.

## Recommendation

Keep most of the existing content. Reorganize around the tasks readers want to complete, give longer reference material a separate home, and connect related workflows at the point where readers need them.

The highest-value changes are:

1. Give EPG its own section and sports/AEDs their own section.
2. Put cloud publishing beside output; separate database synchronization and backup from publishing.
3. Put creating and editing layouts before health checks and advanced maintenance.
4. Provide one XC Server path from installation through pairing to a working player connection.
5. Create a discoverable backup/restore guide and fix the orphaned VPS preparation page.
6. Align release labels and downloadable server templates with the actual release channel.

“New page” below often means extracting existing material, not writing it again. Navigation can change without moving the Markdown files or breaking published URLs.

## 1. Home and Start Here

| Current page | Recommendation |
| --- | --- |
| `docs/index.md` | Keep the source → layout → output explanation. Replace “use navigation or search” for existing users with direct task links: update, publish output, maintain layouts, set up automation, use XC Server, restore a backup. Link the logs collection instruction directly to Logs and Diagnostics. |
| `getting-started/first-working-setup.md` | Keep as the primary tutorial. Its actual destination is **cloud-hosted M3U/EPG links**, so use a subtitle or introductory choice that makes this clear. Provide a short local-output branch linking to output settings; readers should not infer that a cloud account is required for every setup. End with links to native scheduling and XC Server as well as external noGUI scheduling. |
| `getting-started/free-vs-pro.md` | Keep the single entitlement table. Link feature names to their instructions, particularly Dummy Guides/AEDs, linked groups, Universal EPG, automation, and backup. Continue marking Pro features within task sections instead of using “Pro” as their primary organizational category. |
| `getting-started/installation.md` | Keep OS subsections together. Add an early “installing an always-on XC Server?” route to the server setup chooser. |
| `getting-started/first-run.md` | Link “recovery guidance” and the backup advice to concrete pages. Keep existing-database migration as a short branch rather than mixing its steps into first-time setup. |
| `getting-started/updating.md` | Keep the desktop update workflow. Link the prerequisite backup to the proposed backup guide and distinguish desktop updates from server/service/container updates. |

Keep the tutorial first in the sidebar, followed by Installation, First Run, Free vs Pro, and Updating. The tutorial itself already supplies the sequential journey; the sidebar does not need to reproduce every tutorial step.

## 2. Sources

| Current page | Recommendation |
| --- | --- |
| `setup/sources-manager.md` | Keep as the source-maintenance reference. Move `Playlist detail sections` back under `Playlist sources`; it currently sits under `Sync on Start` in the heading hierarchy. Extract Dummy Guide inventory into its own EPG page. Link Add Source/Add EPG actions to their tutorials. |
| `setup/playlists.md` | Keep M3U and XC together: the opening choice is useful. Move the long automatic NoGUI checks explanation to the credential-maintenance/scheduling owner and keep a short description plus link here. Add a route to Custom Sources. |
| `setup/playlist-categories.md` | Keep separate. Add a compact comparison linking source category inclusion, New Category Manager, New Channel Manager, and manual channel import. These are related but control different stages. |
| `setup/epg-sources.md` | Move its navigation entry into EPG & Mapping. Put adding, synchronizing, and verifying a source before optional EPG Layout customization. |
| `setup/source-tools.md` | Move from Advanced / Pro Features to Sources; retain the Pro marker. Rename the sidebar entry to something identifiable, such as “Prefix Removal and Tags.” Link it from source editing and channel naming. No split needed for this short page. |

**New page: Custom Sources.** Sources Manager exposes Add Custom Source, but Adding Playlists only covers M3U and XC. The application’s `EditCustomSource.java` has category and channel creation/editing controls, confirming a distinct workflow worth documenting. Cover creating a source, adding categories/channels, and importing them into a layout. Do not mix this up with **Custom** in the Add EPG source selector.

## 3. Build a Layout

| Current page | Recommendation |
| --- | --- |
| `layouts/index.md` | Keep this useful section landing page and link to it from Home. Add the choice between an independent layout and shared linked groups. |
| `layouts/layout-manager.md` | Reorder to Create → Open → Enable/Disable → Duplicate → settings → health/maintenance → Remove. Currently creation comes after health checks and automatic cleanup. Keep the dashboard reference here; link attention-check configuration to Application Settings rather than maintaining the full list twice. Add actual layout file import/export instructions, which the introduction promises but the page does not provide. |
| `layouts/layout-editor.md` | Keep everyday selection, editing, group organization, and saving here. Move programme preview below basic editing, and empty-group health below group options. Extract the substantial Channel Importer workflow into “Importing Channels.” Keep concise routes to mapping, sports presentation, and preview settings. The EPG button reference currently falls inside “Choose the channel name source”; give it its own heading or move it to Channel Mapping. |
| `layouts/new-channel-manager.md` | Keep. Explicitly connect its source-category options to Playlist Categories and Channel Mapping. The distinction between immediate source-category settings and layout-group rules is valuable and should remain prominent. |
| `layouts/new-category-manager.md` | Keep. Cross-link the same category comparison and the manual importer. Group both manager pages under “Keep Layouts Updated,” a clearer task label than “Layout Tools.” |

**New page: Linked Layout Groups.** Extract the existing short explanation and expand it into when to link versus copy, where edits belong, what is inherited, and how to diagnose a broken link. Link from layout duplication, Channel Importer, health warnings, and Custom Sports Groups. Keep layout-file import/export distinct from importing channels from another layout.

**Possible later extraction: Channel Names and Logos.** Naming modes, source prefixes, category Ignore Name Changes, AED names, TXT lookup names, and sports presentation now interact across several pages. A shared explanation would help, but first improve the links; avoid splitting every editor control into a separate page.

## 4. EPG & Mapping — new navigation section

Bring together Adding EPG Sources, Channel Mapping, EPG Browser, Dummy Guides, EPG Formatting, and Universal EPG. This follows the reader’s task more closely than splitting these between Sources, Build a Layout, and Advanced / Pro Features.

| Current material | Recommendation |
| --- | --- |
| `setup/channel-mapping.md` | Make this the main mapping procedure, including search controls. Add links to basic dummy guides when no real guide exists and to formatting/offset guidance when data exists but needs adjustment. Keep a link from Build a Layout. |
| `setup/epg-browser.md` | Keep its strong explanation that browsing inspects an existing mapping. Place immediately after mapping. |
| Dummy inventory in `setup/sources-manager.md` | Extract **Dummy Guides and Inventory**: basic dummy versus AED, create/edit, assign to a channel, find usage, import/export AEDs, remove unused entries. Link from Add EPG, mapping, Layout Editor, and AEDs. |
| EPG Layout in `setup/epg-sources.md` | Extract **EPG Formatting and Overrides**. Explain title/description components and link to layout-level overrides. Confirm the precedence in source before expanding the reference; the current guide mentions both levels without bringing them together. |
| `setup/universal-epg.md` | Keep compact and place here. Add links from output settings and player setup, so readers encounter shared EPG output when choosing what to publish. |

An EPG landing page only needs a short chooser: add programme data → map it → inspect it → format/publish it; use dummy guides/AEDs when that is the intended workflow.

## 5. Output & Users

| Current page | Recommendation |
| --- | --- |
| `layouts/output-settings.md` | Keep as the destination/format reference. Add direct links to cloud authorization, output generation, Universal EPG, and XC setup. Verify its “Edit Layout” step: Layout Manager documents that action as opening Layout Editor, while output settings are described in Layout Manager itself. |
| `setup/output.md` | Keep as the generation procedure. Add a “what next?” route to local-file player setup, cloud links, and per-user output. |
| `layouts/users.md` | Move the “one source per provider” model and credential example ahead of XC password/activity details. Link credential maintenance to NoGUI checks and Email Notifications. Retain desktop-specific controls here and link server-specific controls. |
| `settings/cloud-providers.md` | Move its sidebar entry here. Keep the Dropbox/Google Drive tabs together. It is a prerequisite for publishing, not primarily an automation feature. Link database sync as a separate optional use. |
| `setup/output-links.md` | Move beside cloud provider setup and Creating Output. Add a direct prerequisite link to provider authorization. |

**New page: Connect a Player.** Consolidate the final-mile steps currently scattered across the tutorial, output pages, and user pages. Distinguish local M3U/XMLTV, cloud M3U/XMLTV, and XC login. Explain where to obtain each value, which user/layout it belongs to, and how to verify playlist, guide, and playback. Use generic player fields rather than creating a maintenance burden of player-specific tutorials. Link both desktop and server user pages to it.

## 6. Automation & Cloud

Rename this section **Automation & Sync** after moving publishing to Output & Users.

| Current page | Recommendation |
| --- | --- |
| `settings/scheduling.md` | Put the native scheduler before external scheduling. Link to the server’s Current Schedules section and explain which instructions apply to desktop versus server operation. |
| `settings/automation.md` | Lead with OS setup and testing; place automatic credential-check behavior afterward. Make “Test the task” a Windows subsection or give each OS an explicit verification section. Its current top-level title sounds cross-platform although its steps are Windows-only. |
| `settings/email.md` | Keep with automation. Add incoming links from desktop users, expiry/credential checks, and source access/expiry information. |

Add a short **Choose a Scheduling Method** introduction covering native desktop scheduling, external noGUI scheduling, and XC Server schedules, with links rather than duplicated commands.

**New page: Database Sync and Backups.** Extract the overview from Application Settings, and distinguish publishing output, synchronizing the database, and retaining recoverable backups. Explain how to identify the authoritative database and verify a backup, then route to provider-specific or XC workflows. The existing guide repeatedly asks users to make a backup without one discoverable procedural destination.

## 7. Advanced / Pro Features

Replace this catch-all with **Sports & AEDs**. Move source maintenance to Sources, EPG tools to EPG & Mapping, and AI provider configuration to Settings. Keep Pro labels on the relevant entries.

| Current page | Recommendation |
| --- | --- |
| `features/aed.md` | Split into a practical “Create and Assign an AED” guide, “AED Templates and Regex” reference, and “Manage and Refresh AEDs.” The existing 318-line page combines first use, formatting syntax, inventory, integrity repair, refresh recovery, and bulk tools. Put assignment explicitly in the beginner path. Move database integrity repair to troubleshooting; retain only a brief troubleshooting link in the setup guide. |
| `features/custom-sports.md` | Keep as one workflow. Reorder to create → matching prerequisites → exclusions/filtering → sort/favourites → presentation → verify, reflecting how the final presentation is determined. Keep the related Layout Editor section short and link here. |
| `setup/custom-sports-channel-names.md` | Keep intact and rename the navigation entry “TXT Fixture Names for AEDs” or similarly explicit wording. Add links from playlist source fields, AED setup, and Custom Sports Groups. It currently has no incoming Markdown body links. |
| `settings/ai.md` | Put under Settings, with an adjacent link from the AED regex workflow. Keep provider setup separate from using AI suggestions. |

Add a short sports landing-page example: source channels → optional TXT fixture names → AED match → group filtering/sorting → presentation → output. Explain which steps are optional so readers do not assume every sports group needs a TXT file or AI.

## 8. XC Server

This is the largest navigation subtree. It needs a task hierarchy above its screen-by-screen reference.

### Overview and installation

| Current page | Recommendation |
| --- | --- |
| `server/overview.md` | Make the opening a concept and decision page: what the server does, desktop versus headless, user delivery, and the setup path. Move flags, environment variables, port precedence, and proxy trust to **Runtime Configuration Reference**. Move the linked-series playback edge case to troubleshooting and administrator reset to recovery. |
| `server/setup/index.md` | Keep as the installation chooser. Put release/platform support status in the choice table. Add the missing VPS preparation link. End with one shared first-connection checklist. |
| `server/setup/prepare-vps.md` | Add to navigation and link from native Linux prerequisites. It currently has neither a sidebar entry nor an incoming page link. Keep its 14-step walkthrough together; its length supports a single sequential host-preparation task. Preserve the stated native-Linux scope. |
| `server/setup/docker.md` | Keep the default installation coherent. Make LAN versus public HTTPS an early branch. Consider extracting the bundled-Caddy/existing-proxy material to **Docker HTTPS Setup**, with the network decision made before the reader starts configuring access. |
| `server/setup/docker-advanced.md` | Keep networks, ownership, and NAS variations here. Move backup/restore/upgrade/rollback to a Docker maintenance page, or at minimum add direct task links to these anchors from setup and recovery. |
| `server/setup/linux-service.md` | Give “Enable and start the service” its own heading: it currently falls within “Optional: use a dedicated service account” even though both account choices need it. Move port override reference after the basic service example. |
| `server/setup/macos-service.md` | Keep installation and service commands together; link its backup/upgrade section from the maintenance overview. Preserve the testing-status note until platform verification is complete. |
| `server/setup/windows.md` | Keep the Task Scheduler tabs together. Fix the backup step that says “in the Server Console” but links to the desktop GUI dashboard. Resolve the intended interface before changing the label or destination. Preserve the documented testing/shutdown limitations. |
| `server/setup/direct-https.md` | Keep as a specialist page under Networking/HTTPS, not among the OS installation choices. Add an explicit platform scope: the native instructions cover systemd/macOS, while Windows also points here. |

### Pairing and routine operation

| Current page | Recommendation |
| --- | --- |
| `server/gui-settings.md` | Rename the navigation entry **Connect the Desktop to XC Server**. Put normal pairing before automatic reloads and failure recovery. Extract bootstrap repair into recovery. Keep settings reference below the normal workflow. |
| `server/dashboard.md` | Retain **Desktop Server Dashboard** as a clearly desktop-specific reference. Group controls by status, synchronization, backups, and paired clients; link each to its procedure. |
| `server/index.md` | Keep as the browser console landing page. Prioritize sign-in → editing access → routine operations. Link bootstrap to a dedicated first-connection path. Keep Begin Editing/End Editing and Take Over semantics here as shared prerequisites. |
| `server/console/login.md` | Keep first administrator setup and sign-in together; add links to security setup and administrator recovery. |
| `server/console/settings.md` | Keep address settings; make server scheduling discoverable from Automation & Sync. Extract schedules only if it grows into a substantial procedure. |
| `server/console/users.md` | Keep server-specific user actions. Link the provider/source/user model in Desktop Output Users and the proposed Connect a Player guide. |
| `server/console/activity.md` | Keep and group under Users & Activity. Its link back to user management is useful. |
| `server/console/paired-devices.md` | Keep access management here. Link directly to desktop pairing and pairing recovery; distinguish paired installations from IPTV player users. |
| `server/console/link-restore.md` | Expand into a usable procedure or make it a section in server recovery. The current short description is insufficient as the main bootstrap alternative offered by several setup pages. |
| `server/console/logs.md` | Keep; cross-link global diagnostics and server troubleshooting. |
| `server/console/metadata.md` | Merge into a console status/reference page unless specific interpretation steps justify a separate page. It is currently a screenshot plus a very brief description. |
| `server/console/security.md` | Keep two-factor and PIN configuration together; link administrator-account changes and recovery. |
| `server/console/administrator.md` | Merge into security/account management or keep as a child reference; link reset/recovery from here. Administrator reset is currently buried in Overview. |
| `server/console/api.md` | Group under **API & Integrations**. Keep key creation/scopes/revocation here. Treat paired reload credentials as a short explanatory link to pairing. |
| `server/console/swagger.md` | Rename to **Using the API and Swagger** if retaining the endpoint examples. Make this the owner of specification access/examples; API Keys currently repeats much of the same material. |

The recommended server journey is: choose installation → install → initialize administrator/database → pair desktop → configure layout/user delivery → test in player → schedule and back up.

### Release-day consistency

The shared `includes/xc-server-preview.md` says the server workflow is unreleased and targeted for 3.12 Beta. Navigation, Server Console, Desktop Output Users, and setup text repeat that status. Docker examples and downloaded Compose/environment files use the alpha image channel.

If these workflows are included in the upcoming stable release, update these together once the exact version and image repository/tag are known. If XC remains preview, label that boundary explicitly on the stable guide. Do not simply remove the warning or guess the stable image name. Platform testing status is separate from product release status.

## 9. Settings

| Current page | Recommendation |
| --- | --- |
| `settings/application.md` | Keep as the application-wide reference. Replace its broad backup section with a concise explanation and link to Database Sync and Backups. Own the attention-check setting definitions here; Layout Manager should own their use in diagnosis. Link output tags to output and preview controls to Layout Editor. |
| `settings/preferences.md` | Keep preferences and theme together. Add incoming links from editor display, sorting, and naming workflows. Keep visible which preferences affect output versus appearance. |
| `settings/pro.md` | Keep the short activation procedure; continue linking the canonical entitlement table. |
| `settings/ai.md` | Move navigation here, with links from AED AI assistance. |

An Application Settings page should identify settings and link to task procedures; it should not become a second copy of every workflow controlled by those settings.

## 10. Troubleshooting, recovery, FAQ, and support

| Current page | Recommendation |
| --- | --- |
| `troubleshooting/common-problems.md` | Group symptoms under startup, sources, EPG, layouts/output, and server. Link each answer to the exact corrective procedure. Move lengthy XC reload/cache explanations into server troubleshooting and retain short routes here. |
| `troubleshooting/logs.md` | Keep the common diagnostics checklist. Link application data locations and the browser console’s logs page. |
| `troubleshooting/reset.md` | Merge restore guidance into a canonical restore guide. Preserve a short reset/escalation section and the old URL. The title currently emphasizes reset while most content advises restore. |
| `troubleshooting/recovery.md` | Split application file locations, database restore, and XC pairing recovery. Lead database restore with a chooser for local file, cloud, URL, and XC backup; Dropbox deleted-file recovery becomes a provider-specific branch. The source’s restore labels confirm those four choices. |
| `faq.md` | Keep short answers, but link M3U/XC to Adding Playlists, empty EPG to mapping/troubleshooting, updating to Updating, and support requirements to Logs and Diagnostics. |
| `support.md` | Make this the canonical support routing page and link it from Home, FAQ, and troubleshooting. It currently has no incoming Markdown body links; callers instead repeat the external destinations. |

The reset page says manual deletion requires support instructions, while Recovery provides a full reinstall/deletion procedure. Align these into one decision path so readers do not get different escalation guidance depending on the entry page.

## 11. Changelog

Keep `changelog.md`, but move it below task-oriented navigation or under a Release Notes entry. Add a concise stable-release summary with links to the changed feature instructions. Preserve detailed build history in an archive if the page keeps growing.

Move migration/foreign-key/cache internals out of normal how-to introductions and leave those details in release notes or targeted diagnostics. Readers generally need the symptom, expected behavior, and corrective action.

## Proposed navigation shape

```text
Home
Start Here
Sources
Build a Layout
EPG & Mapping
Output & Users
Sports & AEDs · PRO
Automation & Sync
XC Server · PRO
  Overview
  Install
  Connect & Pair
  Users & Player Access
  Operate & Back Up
  Security & Recovery
  API & Integrations
  Configuration Reference
Settings
Troubleshooting & Recovery
FAQ
Support
Release Notes
```

Avoid a simultaneous folder reshuffle. First change `mkdocs.yml`, extract only the most useful pages, and update incoming links. For extracted sections, retain a short link at the old heading so existing deep links continue to work. If a page is ultimately removed, provide a redirect or a useful pointer at its old URL.

## Implementation order

### Before stable release

- Resolve stable/preview/channel labeling across pages and downloads.
- Expose VPS preparation; fix the Windows backup interface link and Linux service heading hierarchy.
- Reorder Layout Manager and Layout Editor around first use.
- Group EPG tools and cloud output by task.
- Add the missing contextual links, especially TXT fixture names, email, source tools, support, and backup prerequisites.
- Provide a backup/restore entry point and an end-to-end XC/player path.

### Next documentation pass

- Extract Channel Importer, linked groups, Dummy Guides, and AED task/reference pages.
- Add Custom Sources and EPG formatting/override procedures.
- Consolidate recovery, server operations, and API duplication.
- Add release-note summaries and archive older build history as needed.

## Verification and limits

- Inspected all 65 guide Markdown pages and the configured navigation.
- A local Markdown body-link target check found no missing destination files. This does not validate heading anchors, images, external URLs, or whether a link leads to the correct workflow.
- `server/setup/prepare-vps.md` is the only guide Markdown page absent from navigation and has no incoming Markdown body links.
- Several useful pages are sidebar-only destinations: TXT fixture names, source tools, email, preferences, and support. This is a contextual-discovery issue rather than a broken-link error.
- The strict MkDocs build could not run: `.venv/bin/mkdocs` is missing and there is no `mkdocs` executable on PATH. No rendered-site or platform execution validation is claimed.
- This review adds only this report. The pre-existing modification to `docs/layouts/layout-editor.md` was preserved.
