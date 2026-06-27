# Security policy

## Reporting a vulnerability

**Please do not report security issues in public GitHub Issues or Discussions.**

Instead, use **[GitHub's private vulnerability reporting](https://github.com/rhybad/gog-vault/security/advisories/new)**
(Security → "Report a vulnerability" on this repository). That keeps the details private until a fix is
available.

When you report, please include:

- A description of the issue and its potential impact.
- Steps to reproduce (a proof-of-concept if you have one).
- The version affected (`GV_VERSION` / tag) and your environment.

### What to expect

This is a **hobby project maintained by one person**, so there is **no guaranteed response time or fix
timeline**. Reports are taken seriously and handled on a best-effort basis. Please allow a reasonable
window before any public disclosure so a fix can be prepared.

## Good practices when self-hosting

GOG Vault is meant to run on a **private network** (home/NAS/LAN), not exposed directly to the internet.
A few things that keep you safe:

- **Don't publish it raw to the internet.** If you need remote access, put it behind a reverse proxy with
  HTTPS and authentication, or reach it over a VPN. See [`docs/reverse-proxy.md`](docs/reverse-proxy.md).
- **Keep `APP_ENCRYPTION_KEY` and `POSTGRES_PASSWORD` secret.** They live in your `.env` — never commit
  that file. Your GOG tokens are encrypted at rest with `APP_ENCRYPTION_KEY`.
- **Keep images current** (`docker compose pull`) so you get fixes.
- Only the web UI port is published; the database, Redis, and API stay on the internal Docker network.

## Scope

This policy covers the published GOG Vault container images and this repository's contents. It does not
cover GOG's own services, your host OS, Docker, or your reverse proxy — secure those per their own
guidance.
