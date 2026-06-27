# Getting Started

A step-by-step walkthrough — no prior Docker experience assumed. If you're already comfortable with
Docker Compose, the [Quick start in the README](../README.md#-quick-start) is the short version.

## 1. What you need

- **A machine that stays on** when you want backups to run — a NAS, home server, mini-PC, or a VPS.
- **Docker** with the **Compose v2** plugin (the `docker compose` command — note the space, not the
  older `docker-compose` with a hyphen).
- **Disk space** for your library on a path you choose (`ARCHIVE_HOST_PATH`).

The images are multi-arch, so **amd64** (most PCs/NAS) and **arm64** (Raspberry Pi 4/5, Apple-silicon
hosts) both work.

## 2. Install Docker (skip if you already have it)

- **Synology / QNAP / Unraid:** install "Container Manager" / "Docker" from your NAS app center; it
  includes Compose. Most NAS UIs can import a `docker-compose.yml` directly.
- **Linux:** follow the official [Docker Engine install](https://docs.docker.com/engine/install/) and the
  [Compose plugin](https://docs.docker.com/compose/install/linux/).
- **Windows / macOS (for a test):** install [Docker Desktop](https://docs.docker.com/desktop/).

Verify it works:

```bash
docker --version
docker compose version
```

Both should print a version. If `docker compose version` errors, install the Compose v2 plugin.

## 3. Get the config files

```bash
mkdir gog-vault && cd gog-vault
curl -O https://raw.githubusercontent.com/rhybad/gog-vault/main/docker-compose.yml
curl -O https://raw.githubusercontent.com/rhybad/gog-vault/main/.env.example
```

(No `curl`? Just download those two files from this repo and put them in a folder.)

## 4. Create your `.env`

```bash
cp .env.example .env
```

Open `.env` in any text editor and set the **two required values**:

- **`POSTGRES_PASSWORD`** — any strong password. It's only used inside the stack.
- **`APP_ENCRYPTION_KEY`** — a 32-byte base64 key that encrypts your GOG tokens at rest. Generate one:

  ```bash
  openssl rand -base64 32
  ```

  Copy the whole output as the value (e.g. `APP_ENCRYPTION_KEY=Yl3...=`).

Optionally point **`ARCHIVE_HOST_PATH`** at a big disk or NAS share (default is `./archive` next to the
compose file). Full list of settings: [Configuration](configuration.md).

> ⚠️ Keep `.env` private and never commit it — it holds your secrets.

## 5. Start it

```bash
docker compose up -d
```

The first run downloads the images and sets up the database automatically — give it a minute or two. Watch
progress with:

```bash
docker compose logs -f
```

Check everything is healthy:

```bash
docker compose ps
```

## 6. Open the dashboard & connect GOG

1. Browse to **`http://<your-host>:8080`** (use your server's LAN IP or hostname; `localhost` if you're on
   the same machine).
2. Click **Connect GOG** and sign in through the in-app flow. Your tokens are stored **encrypted** with
   your `APP_ENCRYPTION_KEY`.
3. Run your first **library sync**, then back up the titles you want.

That's it — head to **[Using the dashboard](usage.md)** to learn the day-to-day features, or set up a
schedule so it runs on its own.

## Stopping / restarting

```bash
docker compose stop     # stop (keeps data)
docker compose start    # start again
docker compose down     # stop and remove containers (your db + archive folders and data are kept)
```
