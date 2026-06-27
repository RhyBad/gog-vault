# Troubleshooting

Most first-run problems are one of these. If yours isn't here, search
[existing issues](https://github.com/rhybad/gog-vault/issues), ask in
[Discussions](https://github.com/rhybad/gog-vault/discussions), or
[open a bug report](https://github.com/rhybad/gog-vault/issues/new/choose).

First, always check the logs:

```bash
docker compose ps
docker compose logs --tail=200 api worker web
```

---

### "POSTGRES_PASSWORD / APP_ENCRYPTION_KEY not set" on `up`

The stack refuses to start without the two required values. Edit `.env` and set both
(`APP_ENCRYPTION_KEY` = `openssl rand -base64 32`). See [Configuration](configuration.md).

### Web UI loads but shows errors / blank, or 502

On **first boot** the `api` container runs database migrations before it's ready — give it a minute.
Watch it become healthy:

```bash
docker compose logs -f api
docker compose ps        # api should be "healthy"
```

If it stays unhealthy, read the `api` logs for the real error (often a database connection or a bad
`DATABASE_URL`/password mismatch).

### `docker compose version` errors / `docker-compose: command not found`

You need **Compose v2** (the `docker compose` subcommand, with a space). Install the Compose plugin —
see [Getting Started](getting-started.md#2-install-docker-skip-if-you-already-have-it).

### Permission denied writing backups

The host folder behind `ARCHIVE_HOST_PATH` must be writable by Docker. Make sure the path exists and the
Docker user can write to it (on a NAS, give the container/share write permission).

### Can't connect my GOG account / login keeps failing

- Make sure your system clock is roughly correct — large clock skew can confuse token expiry (the app
  tolerates minor skew, but a wildly wrong clock is a problem).
- If you previously connected and it now says **"re-authentication needed,"** just reconnect — the refresh
  token couldn't be renewed.

### Live progress looks frozen behind my reverse proxy

Your proxy is buffering the SSE stream. Disable buffering / raise the timeout on `/api/events` — see
[Reverse proxy](reverse-proxy.md).

### Downloads are slow or stop

GOG throttles per file, and your connection is the ceiling. You can raise `DOWNLOAD_CONCURRENCY` (1–8) in
`.env`, but very high values won't necessarily help and may trip GOG's throttling.

### A backup is flagged corrupt / missing

Use the **Integrity** screen to verify, then **repair** — it re-fetches only the bad/missing pieces. Note
that versions GOG has already removed from your account can't be recovered.

### How do I completely reset?

```bash
docker compose down -v          # removes only the Redis named volume
rm -rf ./db                      # delete the database (adjust if you changed DB_HOST_PATH)
```

Your `ARCHIVE_HOST_PATH` and `DB_HOST_PATH` are host folders, so `-v` leaves them alone — delete `./db`
yourself for a full reset. Bring it back up with `docker compose up -d` and reconnect GOG.

---

When reporting a bug, please include your `GV_VERSION`, host OS + architecture, and the relevant log
output (redact anything sensitive). See [`../CONTRIBUTING.md`](../CONTRIBUTING.md).
