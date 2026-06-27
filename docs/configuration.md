# Configuration

All configuration is done through environment variables in your **`.env`** file (Docker Compose reads it
automatically). Start from [`.env.example`](../.env.example): `cp .env.example .env`.

Only **two** values are required. Everything else has a sensible default.

## Required

| Variable             | What it does                                                                                                                                                           |
| -------------------- | --------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `POSTGRES_PASSWORD`  | Password for the bundled PostgreSQL database. Used only inside the stack — pick any strong value. The stack **refuses to start** without it.                            |
| `APP_ENCRYPTION_KEY` | Base64-encoded **32-byte** key used to encrypt your GOG tokens at rest (AES-256-GCM). Generate with `openssl rand -base64 32`. **Used by both the api and the worker.** |

> **About `APP_ENCRYPTION_KEY`:** changing it invalidates already-stored tokens — no data loss, you'd
> just reconnect your GOG account. Keep it (and `.env`) secret and backed up.

## Image / network

| Variable     | Default  | What it does                                                                            |
| ------------ | -------- | --------------------------------------------------------------------------------------- |
| `GV_VERSION` | `latest` | Which published image tag to run. Pin a release (e.g. `v0.1.0`) or track `latest`.      |
| `WEB_PORT`   | `8080`   | Host port for the web UI → `http://<your-host>:<WEB_PORT>`. Only this port is published. |

## Storage

| Variable            | Default     | What it does                                                                                                |
| ------------------- | ----------- | ---------------------------------------------------------------------------------------------------------- |
| `ARCHIVE_HOST_PATH` | `./archive` | Host path where **game backups + archived cover/poster/background art** are written. Point at a big disk / NAS share. Created on first run. The worker writes here; the api reads it (read-only) to stream restores. |

## Database (optional)

| Variable        | Default    | What it does                |
| --------------- | ---------- | --------------------------- |
| `POSTGRES_USER` | `gogvault` | Database user (internal).   |
| `POSTGRES_DB`   | `gogvault` | Database name (internal).   |

## Downloads (optional)

| Variable               | Default | What it does                                                              |
| ---------------------- | ------- | ------------------------------------------------------------------------ |
| `DOWNLOAD_CONCURRENCY` | `3`     | How many files download at once (1–8). GOG also throttles per file.       |

## Advanced (rarely needed)

| Variable            | Default      | What it does                                                                                                       |
| ------------------- | ------------ | ----------------------------------------------------------------------------------------------------------------- |
| `GOG_CLIENT_ID`     | _(built-in)_ | The GOG Galaxy OAuth client id. This is the **publicly-known constant every GOG tool uses** and is baked in — override only if you have a specific reason. |
| `GOG_CLIENT_SECRET` | _(built-in)_ | Matching client secret (same note as above).                                                                      |

## Where your data lives

- **Your archive** (`ARCHIVE_HOST_PATH`) — the part you care about. Back it up independently.
- **Database & Redis** — Docker **named volumes** (`gog-vault_db-data`, `gog-vault_redis-data`). The
  database holds your library metadata and your encrypted GOG tokens.

`docker compose down` keeps both the named volumes and your archive; `docker compose down -v` would
**delete the named volumes** (database + Redis) — your `ARCHIVE_HOST_PATH` is a host folder and is not
touched by `-v`.
