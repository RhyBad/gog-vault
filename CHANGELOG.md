# Changelog

All notable changes to GOG Vault are documented here.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

> ⚠️ **Beta / pre-release (v0.x).** GOG Vault is still early and changing fast, and updates may include
> **one-way database migrations**. Back up before upgrading — see [Updating](docs/updating.md).

## [0.8.0] - 2026-06-30

### Added

- **Filter, sort & search your Library.** A new facet bar lets you narrow the Library by **genre,
  backup status, type, and platform**, with live result counts. Search now also matches **developer and
  publisher** (not just the title), and you can sort by **release date** or **most recently backed up**.
  Your filters and sort are saved in the URL, so a filtered view is bookmarkable and survives a reload.
  On phones the same filters live in the Filter sheet.

### Fixed

- **A "Sync & back up" run now finishes cleanly.** Packs that only bundle other games — with no files of
  their own — were stuck "Not backed up" and re-queued on every run, so a run never reached a clean "all
  backed up" state. They're now treated as having nothing to back up.
- **Packs whose bonus content GOG won't serve no longer churn.** Some editions (e.g. a GOTY / Deluxe
  pack) list extras that GOG returns an error for at the pack — the real files live under the base game.
  These are now marked **Unavailable** and skipped, instead of being retried on every run.
- **A pack's detail popup now matches its card.** A pack with un-backed-up games inside could show a
  misleading "Up to date" or block backing those games up. The popup now reflects the whole pack, and a
  pack's one-click Repair now covers its bundled games too.
- **New versions show up right after you upgrade.** The app page is no longer cached by the browser, so
  a reload always loads the version you just deployed.

## [0.7.0] - 2026-06-29

### Added

- **One-click "Sync & back up" on the Overview.** A new primary button syncs your library and then
  backs up every game that needs it, after a quick confirm showing the estimated download size and game
  count. It runs in the background with the same live per-game progress as a scheduled run, and you can
  watch or stop it on the Activity page.

### Changed

- **New installs now default to backing up English only** (the content-language filter default is now
  English, instead of English + Korean). This only affects fresh installs — existing setups keep the
  languages they already selected, adjustable anytime in Settings.

### Fixed

- **The "estimated full size" no longer over-counts editions you don't back up.** It used to sum every
  platform and language GOG offers, so a game sold in many languages was counted several times and the
  Overview/Storage "archived %" gauge could never reach 100%. It now counts only what your backup scope
  actually downloads.

## [0.6.0] - 2026-06-29

### Added

- **A pack's own files are now visible and reachable.** Some packs (e.g. a GOTY edition) ship their
  own installer/extras on top of bundling games — these were easy to miss. The expanded pack now leads
  with a **"Pack files"** tile that opens the pack's detail view, where its installers and extras are
  listed.

### Changed

- **A pack's status badge now reflects everything it carries** — its bundled games _and_ its own files
  — and is shown consistently on both the Collections page and the Library's flat view (previously each
  screen could show a different badge, and a pack with an un-backed-up own installer could misleadingly
  read "Backed up").

### Fixed

- **Repair now completes a partially-backed-up game** instead of doing nothing. A backup stopped
  partway (canceled, or interrupted) left its un-downloaded files invisible to Repair, so clicking
  Repair finished instantly without fetching anything. Repair now pulls every missing file (and still
  chunk-repairs corrupted ones), making the game whole.
- **A stopped/incomplete backup is no longer silently re-marked "Backed up" on the next library
  sync.** Status is recomputed from every planned file, so an incomplete game correctly stays
  "Partial" until its missing files are actually downloaded.
- **Activity reads "Nothing to repair" / "Nothing to back up"** for a run that had nothing to do,
  instead of a misleading "0/0 files ok".

## [0.5.0] - 2026-06-28

### Added

- **Parallel downloads — back up multiple games at once.** A new Settings → General control (1–4,
  default 1) sets how many games download at once; raise it only on SSD/fast storage. Repair and
  verify still run one at a time.

### Changed

- **Clearer, unified GOG connect / re-authenticate flow.** Onboarding and the sidebar now share one
  dialog that spells out the easy-to-miss step: after you sign in, copy the address-bar URL and paste
  it back (the page itself shows no code).

### Fixed

- **Activity Queue action buttons (e.g. "Cancel run") are no longer clipped** in any theme or screen
  width.
- **Destructive confirm buttons (Disconnect, delete) now show as solid red in the Notch themes**
  instead of a neutral grey.
- **The Disconnect dialog's text no longer has awkward gaps or broken line wraps**, and reads more
  clearly.

## [0.4.2] - 2026-06-28

### Added

- **Schedule times now run in your timezone.** A new `TZ` setting (default `UTC`) controls the zone
  your schedule's cron times are interpreted in — set `TZ=Asia/Seoul` and `0 3 * * *` fires at 03:00
  local, matching the Schedule card's "Next run" wall-clock instead of firing in UTC. It's documented
  in `.env.example`; works on the existing images (no rebuild needed).

### Fixed

- **The scheduled-run progress bar in the Activity Queue now matches its "X of Y" label.** The bar was
  driven by an internal phase split (sync = 0–50%, backup = 50–100%), so it jumped to ~50% the instant
  backup started and read ~51% at game 2 of 59 — contradicting the count beside it. It now tracks
  games-done (game 2/59 → ~3%), and shows an indeterminate bar during the brief sync precursor.
- **Activity Queue per-game rows no longer overlap in non-Korean languages.** The status badge and
  Stop/Remove button tracks were sized for the short Korean labels, so a longer translation (e.g.
  "Remove") could overflow and overlap the status badge — both tracks are now widened and their
  contents clip within their own column.
- **Restore popup file list restyled.** The per-row download button now renders correctly (icon no
  longer vanishes, label centered) as a design-system button, the dead per-row "Copy path" button was
  removed, every cell stays inside its track at any text length, and the full file name shows on hover.
- **Overview "Recent activity" now matches the Activity page design.** The rows were rebuilt on the
  same shared building blocks (type tags, status pills) so the two surfaces read as one design, and the
  missing "Sync + Backup" type label was added (no more raw enum text).

## [0.4.1] - 2026-06-28

### Added

- **Collection packs now show a backup-status chip derived from their owned items** — a pack reads as
  Backed up / Partial / Update available / Corrupt based on the games and DLC it bundles, and the chip
  updates live as those items finish backing up.

### Changed

- **A faster, lighter web app.** A batch of performance improvements: the UI font now loads as small
  on-demand subsets instead of one ~2 MB file; text and JSON responses are gzip-compressed and hashed
  assets are cached immutably; the Library grid no longer re-renders every card on each
  download-progress tick; rapid background refreshes are coalesced into one; and the worker throttles
  download-progress updates — together making the dashboard quicker to load and smoother while a backup
  is running.
- **The Library page no longer shows a separate scheduled-run progress banner.** The per-game cover-card
  progress bars and the Activity Queue already show a scheduled run's progress, so the redundant banner
  was removed.
- **Faster Docker image rebuilds from source** — the build now reuses the cached dependency-install and
  Prisma-client-generate layers when only application code changes (relevant only if you build the
  images yourself; the published images are unaffected).

### Fixed

- **Verify and Repair on the Integrity page now show their result.** Like the game-detail popup, the
  Integrity page now surfaces a result banner after a check — the verify counts (ok / corrupt / missing)
  or the backup·repair roll-up — instead of running silently with no visible outcome.

### Removed

- **The non-functional "Concurrent downloads" setting** has been removed. It was adjustable but had no
  effect — backups always run one at a time (a deliberate courtesy to GOG's servers) — so it only caused
  confusion. The underlying database column was dropped via a migration applied automatically on upgrade.

## [0.4.0] - 2026-06-28

### Added

- **Activity now shows a live download Queue — a built-in download manager.** While a backup is in flight,
  the Activity page shows a **Queue** section at the top — the active scheduled run with its per-game
  children (downloading → live progress bar + **Stop**, waiting → **Remove**) plus any standalone manual
  backups/repairs, with a **Cancel run** action — sitting above your full job **History**. In-flight jobs
  show only in the Queue (they're de-duplicated out of the History list, and reappear there once they
  finish); when nothing is downloading the page is just the History list as before. The topbar "running"
  pill and a new live **count badge on the Activity sidebar item** jump straight to it.
- **Full progress visibility for scheduled backups.** A scheduled run that backs up your library now
  shows exactly what it's doing: each game lights up on its **Library cover card** with a live download
  bar (just like a manual backup), and a banner shows an **"X of Y"** count of games backed up so far.
  The run also appears in **Activity** as a parent that expands to the per-game backups nested beneath it.
- **Every queued game shows its status, not just the one downloading.** While a backup (manual batch or
  scheduled run) works through your library, each game waiting its turn shows a **"queued"** badge on its
  Library card — and on the nested **DLC rows in the game-detail popup** — turning into a live progress
  bar when its turn comes. The status survives a page reload.
- **Stop a running scheduled backup.** The run banner has a **Stop** button that cancels the whole run —
  the in-progress game is stopped and the not-yet-started games are skipped. A single game that fails or
  is stopped no longer aborts the rest of the run.

### Changed

- **A manual backup of a game that's already backing up is now coalesced** instead of starting a second,
  redundant job — backups run one at a time, so the click simply rides the in-flight backup.
- **A failed backup now clears its card** immediately (with a brief "failed" note) instead of leaving a
  frozen progress bar until the next reload.
- **The database now lives on a configurable host path** (`DB_HOST_PATH`, default `./db`) instead of an
  opaque Docker named volume, so it's easy to find, back up, and snapshot. _Upgrading an existing
  install:_ copy your old `gog-vault_db-data` volume contents into the new path before starting,
  otherwise the database initializes empty (your data is still in the old volume).
- **Game backups are now organized under an `archive/games/` subfolder**, keeping them cleanly separated
  from archived art and UI assets. Existing archives are relocated automatically on the first start after
  upgrading — no re-download needed.

### Fixed

- **Verify and Repair in the game-detail popup now show their result.** The check always ran, but its
  outcome only appeared behind the popup — so it looked like nothing happened, and a Repair with nothing
  to fix looked like it "canceled itself." The popup now shows the result inline above the actions: the
  verify counts (ok / corrupt / missing), the backup·repair roll-up, or "nothing to do." A finished
  Verify also no longer leaves the buttons briefly stuck on **Stop**.
- **Verifying a partially-backed-up game no longer reports it as fully backed up.** If you stop a download
  partway, Verify now correctly accounts for the files that were never downloaded, so the game stays
  **Partial** instead of flipping to a misleading **Backed up**.
- **A partially-backed-up game no longer offers Restore.** Restoring an incomplete backup would lay down a
  broken install, so Restore is now disabled (with an explanation) until the backup is complete.
- A schedule's **"next run" time no longer drifts when you use "Run now"** — a manual run is recorded
  without disturbing the cron schedule's next occurrence.

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
