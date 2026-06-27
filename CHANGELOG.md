# Changelog

All notable changes to GOG Vault are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

> ⚠️ **Beta / pre-release (v0.x).** GOG Vault is still early and changing fast, and updates may include
> **one-way database migrations**. Back up before upgrading — see [Updating](docs/updating.md).

## [0.3.0] - 2026-06-27

### Added

- **Themes** — a new **Theme** picker in **Settings → General** restyles the whole dashboard. Choose
  **Inkwell** (the new default), **Voltage**, **Sherbet**, **Patina**, **Ember**, or **Orchid** — six
  new chamfered **Notch** palettes — or **Classic**, the original flat look. Your choice is saved to
  your account, so it follows you across devices and browsers, and it applies instantly with no flash
  on load.
- **Custom background image** — also in **Settings → General**, upload your own image to display behind
  the main content area, with a dim slider so text stays readable. The sidebar and cards/tables stay
  opaque, and (like the theme) the background follows you across devices. Uploads are capped at 20 MB.
- **Support link** — the sidebar footer now shows a **Support** link beside the app version that opens
  the project on GitHub, for reporting issues and getting help.

### Changed

- **English is now the default interface language**, and the first option in **Settings → Display
  language**. The app falls back to English instead of Korean when your browser's language isn't one of
  the supported options; Korean is still auto-detected and selectable.
- **New and existing dashboards now default to the Inkwell theme** (a Notch palette), which leads the
  theme picker, with **Classic** second. A dashboard that has never had a theme set adopts Inkwell on
  upgrade — pick **Classic** to keep the previous flat appearance.
- The self-host Compose example now defaults the images to `:latest` (set `GV_VERSION` to a specific
  `vX.Y.Z` tag instead for reproducible, deliberate upgrades).

## [0.2.0] - 2026-06-26

### Added

- **Open a game's detail from anywhere.** A game's detail now opens as an overlay on top of whatever
  page you're on — including directly from the items listed inside a **Collection** — with no
  full-page jump or flash, and the browser Back button closes it.

### Changed

- **Game-detail actions now match the game's state.** The buttons in a game's detail view adapt to
  what actually applies: the primary button becomes **Back up / Resume / Update / Repair / Stop** (or a
  disabled **Up to date**) to fit the game's status; actions that can't apply — Restore with nothing
  backed up, Repair on a healthy backup, Verify/Repair while a job is already running — are disabled
  with a tooltip explaining why; and a fully backed-up game gains a **Re-download** button to fetch a
  fresh copy. The previously always-clickable buttons could silently do nothing or error.
- **Archived game art refreshes when GOG changes it.** Image archival now re-fetches a game's cover,
  poster, or background as soon as GOG rotates that image's source URL — not only on the 30-day refresh
  timer — and an image that failed to archive retries on the next sync instead of waiting out that
  window. Your locally-archived art tracks GOG's current art more closely.
- **Notifications & activity summaries now follow your language.** Webhook notifications (Discord/Slack/
  ntfy) and the run summaries in the **Activity** view are now localized (Korean/English) — your
  **Settings → Display language** choice drives the UI _and_ these backend messages. The technical
  **Logs** stream stays English (easier to search and to attach to a bug report).

### Fixed

- A game that was actively downloading could show **"waiting / concurrency limit"** on its library
  cover card for the entire first (or only) file; the card now tracks live download percentage as the
  bytes arrive.
- The **Storage** trend chart's y-axis labels no longer clip on narrow/mobile screens.
- **Activity** log type tags now align into a uniform column.

## [0.1.0] - 2026-06-25

### Added

- **Mobile & tablet layout** — the dashboard is now responsive. On phones the sidebar collapses into a
  hamburger drawer, the game-detail view opens as a full page, dense tables and modals reflow into
  stacked cards and bottom sheets, stat/poster grids reflow to fit, and touch targets are enlarged;
  tablets keep the sidebar with a fluid content area. The desktop layout (≥1180px) is unchanged; the
  1024–1179px range, previously locked to a fixed-width shell, now flexes too.
- **Game image archival** — during sync the worker now saves each game's cover, poster, and background
  art to your archive volume (refreshing the copy when it goes stale), and the app falls back to the
  archived copy when GOG's live image URL fails to load. Your library art keeps working through GOG
  CDN/URL changes.
- **Public self-host bundle** — a ready-to-run `docker-compose.yml` that pulls the published images (no
  source checkout or build required), an annotated `.env.example`, and quick-start docs, so you can
  stand up GOG Vault from the public images alone.

### Changed

- Container images are now published to the public **GitHub Container Registry**
  (`ghcr.io/rhybad/gog-vault-{api,worker,web}`), so you can self-host straight from the public images —
  pin a version tag or track `:latest`.

## [0.0.3] - 2026-06-25

### Added

- **Library restructure** — the library is now Plex-style: a grouped view shows your base games
  (with each game's DLC and bonus content nested in its detail page) and a flat-view toggle still
  lists every product. A new **Collections** tab presents your GOG packs/bundles, each expandable to
  the games and DLC it contains.
- **English interface** — the UI is now localized (Korean and English), auto-detected from your
  browser and switchable under **Settings → Display language**. Community languages are welcome.
- **Deep linking & browser navigation** — sections, games, and collections each have their own URL,
  so links are shareable and the browser Back button works (e.g. game → DLC → back).
- **Steadier GOG sign-in** — manual and automatic token refresh, tolerance for container clock skew
  in the "expired" state, a clear "re-authentication needed" state, and your GOG username shown in
  place of the numeric id.

### Changed

- Backup status is far more legible on cover cards: a status-colored border plus a status chip,
  including live backup/verify progress.
- Collections show landscape pack art, and each pack lists the games and DLC it bundles (resolved
  from GOG's own bundle membership).
- Container images are now built for both **linux/amd64 and linux/arm64**.

### Fixed

- A DLC thumbnail could expand to fill the entire game-detail popup.
- The live "working" pulse faded the whole cover card; it is now confined to the status badge.
- Removed an unintended underline on the sidebar navigation items.
- Collections packs with no owned sub-items now show their cover art instead of a blank placeholder.

## [0.0.2] - 2026-06-24

### Changed

- Slimmed the `api` and `worker` Docker images (production-only dependency trees; every compressed
  layer now under 100 MB) and moved CI to a dedicated, faster self-hosted runner.

## [0.0.1] - 2026-06-24

### Added

- Initial release. Self-hosted (Docker) dashboard to back up, verify, restore, and auto-sync your
  own DRM-free GOG installers: library sync, downloads/backup, integrity verify + repair, scope
  filters, scheduler, update detection, webhook notifications, storage insights, activity log,
  restore helper, and cover-art/metadata. Licensed Apache-2.0.
</content>
</invoke>
