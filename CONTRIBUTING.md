# Contributing & feedback

Thanks for taking the time! First, a bit of context that shapes how this repo works.

## How this project is built

GOG Vault is a **personal hobby project**, developed largely through **"vibe coding"** — fast, iterative
work with heavy help from AI coding agents — rather than by a team following a long traditional process.
The **application source code is private**; this public repository exists to distribute the prebuilt
container images, host the documentation, and collect your feedback.

What that means for you:

- **You can't send code pull requests here** — there's no source in this repo. (Typo fixes or doc
  improvements to the Markdown files _are_ welcome as PRs, though.)
- The best way to help is a **clear, reproducible bug report** or a **well-described feature idea**.
- Please be patient and kind. One person maintains this in their spare time; some issues may take a
  while, and some may be closed if they can't be reproduced or are out of scope for a hobby effort.

## 🐛 Reporting a bug

Before opening an issue:

1. **Search [existing issues](https://github.com/rhybad/gog-vault/issues)** — it may already be known.
2. **Skim [Troubleshooting](docs/troubleshooting.md)** — many first-run problems are covered there.
3. **Try `:latest`** (or the newest release) — it may already be fixed.

Then [open a bug report](https://github.com/rhybad/gog-vault/issues/new/choose) and include:

- The **version** you're running (`GV_VERSION`, or the tag/`latest`).
- Your **host** (OS + architecture — amd64 / arm64) and that you're on **Docker Compose v2**.
- **What happened vs. what you expected**, and the **exact steps** to reproduce.
- **Relevant logs**: `docker compose logs --tail=200 api worker` (redact anything sensitive).

The more reproducible it is, the more likely it gets fixed.

## 💡 Suggesting a feature

[Open a feature request](https://github.com/rhybad/gog-vault/issues/new/choose) or start an
**[Idea discussion](https://github.com/rhybad/gog-vault/discussions)**. Tell us the **problem you're
trying to solve**, not just the solution — that makes it much easier to find the right fix. No promises
on what gets built, but good ideas are genuinely appreciated.

## 💬 Questions & help

Use **[Discussions](https://github.com/rhybad/gog-vault/discussions)** for setup help and general
questions — that keeps the issue tracker focused on bugs and concrete feature requests.

## 🔒 Security issues

**Don't** file security problems as public issues — see [`SECURITY.md`](SECURITY.md).

## Scope & expectations

This stays a focused, personal tool for backing up **your own** GOG library. Requests that turn it into a
launcher, bypass DRM, or otherwise fall outside "personal archival of installers you own" are out of
scope and will be declined. Thanks for understanding. 🙇
