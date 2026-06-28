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
| `GV_VERSION` | `latest` | Which published image tag to run. Pin a release (e.g. `v0.5.0`) or track `latest`.      |
| `WEB_PORT`   | `8080`   | Host port for the web UI → `http://<your-host>:<WEB_PORT>`. Only this port is published. |

## Storage

| Variable            | Default     | What it does                                                                                                |
| ------------------- | ----------- | ---------------------------------------------------------------------------------------------------------- |
| `ARCHIVE_HOST_PATH` | `./archive` | Host path where **game backups + archived cover/poster/background art** are written. Point at a big disk / NAS share. Created on first run. The worker writes here; the api reads it (read-only) to stream restores. |
| `DB_HOST_PATH`      | `./db`      | Host path for the **PostgreSQL data dir** (library metadata + your encrypted GOG tokens). Small but precious — back it up. Created on first run; Postgres stores under a `pgdata/` subdir of it. |

## Database (optional)

| Variable        | Default    | What it does                |
| --------------- | ---------- | --------------------------- |
| `POSTGRES_USER` | `gogvault` | Database user (internal).   |
| `POSTGRES_DB`   | `gogvault` | Database name (internal).   |

## Schedules & time (optional)

| Variable | Default | What it does                                                                                                                                                              |
| -------- | ------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| `TZ`     | `UTC`   | Timezone (applied to the api + worker) that your **schedule's cron times are interpreted in**. Set your local zone — e.g. `Asia/Seoul`, `America/New_York`, `Europe/Berlin` — so `0 3 * * *` fires at 3 AM your time, not 3 AM UTC. Takes effect on container restart; no rebuild needed. |

> Backups always run **one at a time** (a deliberate courtesy to GOG's servers), so there's no
> download-concurrency setting to tune.

## Advanced (rarely needed)

| Variable            | Default      | What it does                                                                                                       |
| ------------------- | ------------ | ----------------------------------------------------------------------------------------------------------------- |
| `GOG_CLIENT_ID`     | _(built-in)_ | The GOG Galaxy OAuth client id. This is the **publicly-known constant every GOG tool uses** and is baked in — override only if you have a specific reason. |
| `GOG_CLIENT_SECRET` | _(built-in)_ | Matching client secret (same note as above).                                                                      |

## Where your data lives

- **Your archive** (`ARCHIVE_HOST_PATH`) — the part you care about. Back it up independently.
- **Database** (`DB_HOST_PATH`, default `./db`) — a host folder holding your library metadata and your
  encrypted GOG tokens. Small but precious; back it up.
- **Redis** — a Docker **named volume** (`gog-vault_redis-data`), transient queue/progress state.

`docker compose down` keeps everything; `docker compose down -v` deletes only the **Redis** named volume
now — your `ARCHIVE_HOST_PATH` and `DB_HOST_PATH` are host folders and are **not** touched by `-v`.
