# Using the dashboard

Once the stack is up and you've **connected your GOG account** (see
[Getting Started](getting-started.md)), here's how the day-to-day features work.

> See the [screenshots in the README](../README.md#-screenshots) for what these screens look like.

## Connect & stay connected

- **Connect GOG** signs you in through a guided flow and stores your tokens **encrypted at rest**.
- Tokens refresh automatically. If a session truly can't be renewed, the UI shows a clear
  **"re-authentication needed"** state — just reconnect. Your GOG username is shown once connected.

## Library

- Sync pulls your owned **games, DLC, and packs**. The **Library** is Plex-style:
  - **Grouped** view shows base games with their DLC/extras nested in each game's detail page.
  - **Flat** view (toggle) lists every product.
  - **Collections** is a separate tab of your GOG packs/bundles, each expandable to what it contains.
- Each cover shows a **backup-status badge** (and live progress while a backup/verify is running).

## Back up

- Pick the titles to archive and start a backup. The worker downloads your installers and bonus content
  into your archive volume.
- **Update detection** flags when GOG has a newer build than your local copy, so you can re-grab it.
- **Scope filters** (per game or globally) let you choose platforms / languages / content types so you
  only download what you want.

## Verify & repair

- The **Integrity** screen checksums your archive against GOG's manifests and shows what's **OK,
  corrupt, or missing**.
- **One-click repair** re-fetches just the bad/missing pieces — no full re-download.

## Restore

- The **Restore helper** streams any archived file back out to you when you need to reinstall — no digging
  through folders.

## Schedule & notifications

- Set up **automatic syncs/backups** on a schedule under **Settings**, with a run **history** so you can
  see what happened.
- Schedule times run in the container's timezone — set **`TZ`** in `.env` (see
  [Configuration](configuration.md#schedules--time-optional)) so `3 AM` means your local 3 AM, not 3 AM
  UTC. The default is `UTC`.
- **Webhook notifications** can alert you (e.g. to Discord/Slack-style endpoints) on completion or failure.

## Storage insights & activity

- **Storage insights** show how much space your archive uses and where it's going.
- The **Activity** page is your **download manager** plus a full job history:
  - While anything is downloading, a **Queue** appears at the top — the active scheduled run with each
    game's live progress (**Stop** the one downloading, **Remove** the ones still waiting) plus any manual
    backups, and a **Cancel run** button for the whole batch.
  - Below it is your complete **History** of syncs, backups, verifies, and repairs. When nothing is
    downloading, you just see History.
  - A count badge on the **Activity** sidebar item and the **running** pill in the top bar both jump
    straight here.
- During a scheduled backup, every game also shows its status right on its **Library cover** — a live
  progress bar for the one downloading and a **queued** badge for the ones waiting their turn.

## Settings

- **Display language** — English (default) / Korean (more welcome).
- **Theme** — 7 looks (a flat _Classic_ plus six chamfered palettes); your choice follows you across devices.
- **Custom background** — upload an image to sit behind the main content area, with a dim slider so text
  stays readable.
- Schedules, scope defaults, notification webhooks, and connection management all live here.

## Tips

- First sync of a large library takes a while — let it run; progress is live.
- Point `ARCHIVE_HOST_PATH` at storage with room to grow; your archive is the part worth backing up
  independently (keep your own copies — the 3-2-1 rule — as noted in the [README](../README.md)).
