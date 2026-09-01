# Changelog

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

### 🛠️ Reliability

- Improved synchronization locking, database-transition handling, cache cleanup, and recovery logging.
- Existing databases migrate new layout and layout-channel fields automatically.

If you run into an issue after updating, include the exact IPTVBoss version, operating system, reproduction steps, and sanitized logs when asking in [Discord](https://discord.gg/s3kpjP8EgR) or opening a [support ticket](https://members.bosstees.net/).
