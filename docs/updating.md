# Updating

> ⚠️ **Beta software — back up before you update.** GOG Vault is pre-release (v0.x). Updates often apply
> **database schema migrations automatically on boot, and migrations only move forward** — there is no
> guaranteed downgrade. Before upgrading (especially across a version that changes the schema):
>
> 1. **Back up your archive** (`ARCHIVE_HOST_PATH`) — your installers + art, the irreplaceable part.
> 2. **Back up the database** (it's a host folder, `DB_HOST_PATH`, default `./db`) so you can roll back
>    if a migration misbehaves:
>    ```bash
>    docker compose stop                 # stop the stack so the copy is consistent
>    tar czf gv-db-backup.tgz -C ./db .  # adjust ./db if you changed DB_HOST_PATH
>    docker compose start
>    ```
>    (Restore by stopping the stack and extracting that archive back into the `DB_HOST_PATH` folder.)
> 3. **Skim the [CHANGELOG](../CHANGELOG.md)** for the versions you're crossing.
> 4. Prefer **pinning `GV_VERSION`** to a known-good tag and upgrading deliberately, rather than tracking
>    `latest`.
>
> If an update goes wrong, the safest path on a hobby project is to **roll back to your last good version
> and restore the matching database backup**, then
> [report the issue](https://github.com/rhybad/gog-vault/issues/new/choose).

## The usual update

```bash
docker compose pull
docker compose up -d
```

`pull` fetches the newest images for your pinned tag; `up -d` recreates only the containers that changed.
Database schema migrations are applied **automatically on boot** — you don't run anything by hand.

## `latest` vs pinning a release

In `.env`:

- `GV_VERSION=latest` — always pull the newest published build on `docker compose pull`. Simplest.
- `GV_VERSION=v0.1.0` — pin a specific release and upgrade deliberately by bumping the tag. Recommended if
  you'd rather control exactly when you move versions.

After changing `GV_VERSION`, run the two commands above.

## Check what's new

See the **[CHANGELOG](../CHANGELOG.md)** and the
[Releases page](https://github.com/rhybad/gog-vault/releases) before upgrading.

## Roll back

Pin `GV_VERSION` to the previous release tag and `docker compose pull && docker compose up -d`.

> ⚠️ **Downgrades aren't guaranteed to be safe** if a newer version applied a database migration — schema
> changes generally only move forward. Roll back promptly if you hit a problem right after upgrading, and
> if you keep your own database backups, restore a matching one. For a hobby project, the safest path is
> to **report the issue** and stay on the last good version until it's sorted.

## Tidy up old images (optional)

```bash
docker image prune
```

Removes dangling images left behind by upgrades. Your data is untouched.
